---
title: Creating Uniform Portgroups on VSphere Distributed Switch With Ansible
date: 2020-06-05T15:36:17+00:00
draft: true
toc: false
author: Davy
categories:
  - ansible
  - devops
  - vmware
tags:
  - ansible
  - devops
  - dvs
  - vds
  - vmware
slug: creating-uniform-portgroups-vsphere-distributed-switch-ansible
---

If you are a vSphere administrator managing environments with multiple clusters and vSphere Distributed Switches, then you are probably aware that naming portgroups can sometimes be quite a challenge.  
Especially when similar portgroups have to exist on more than one vSphere Distributed Switch (VDS), because you can&#8217;t have duplicate portgroup names within one vCenter. To avoid clutter you want to create those portgroups in an uniform and automated way. Well, at least i do.

For this task I am using Ansible. I am quite aware that Ansible is not always the holy grale or the best fitting solution for a problem, however, in my case it&#8217;s the automation tool of choice and always the first way to go. My goal here is to show you how you can do devops on your vmware infrastructure in a fast, reliable and simple way.

#### Scenario

_The environment and what i would like to achieve:_

  * Environment has one vCenter vc01 with two clusters, cl01 and cl02
  * Each cluster has it&#8217;s own Distributed Virtual Switch, vc01\_dvs01 and vc01\_dvs02
  * Network based on layer2 VLANs. Portgroupname = VLAN##
  * cluster cl01 need access to VLAN10 and VLAN99
  * cluster cl02 need aceess to VLAN10, VLAN11 and VLAN99
  * Naming convention should be [dvs\_name]-[portgroup\_name]

My approach:

  * use ansible
  * one hosts file
  * one file with portgroup configuration per cluster
  * one file with actual task for deploying portgroups

So I went on the Ansible site and found this module: <a rel="noreferrer noopener" href="https://docs.ansible.com/ansible/latest/modules/vmware_dvs_portgroup_module.html" target="_blank">vmware_dvs_portgroup</a>.  
Unfortunately the vmware\_dvs\_portgroup module has create / remove only. So this module is not suitable for changing existing portgroups. Which is fine for my purpose. Be aware of this though, as of yet I have not found a module for changing portgroups.

Based on the scenario I will create four files in my Ansible structure. The hosts file, two group_var files and the task file. Then run the task file and see the desired configuration pop up on my distributed virtual switches. <figure class="wp-block-image size-large is-resized">

<img decoding="async" loading="lazy" src="https://www.codecrusaders.nl/wp-content/uploads/2020/06/image-1.png" alt="" class="wp-image-4095" width="249" height="185" /> <figcaption>Desired filestructre for scenario</figcaption></figure> 

#### The code

First I will create the hosts file which should be in the root of the inventory folder. When you set the file up as in the example below I will have the flexibility to run the task against all `distributed switches` or limit to just one, for e.g. `dvs_cc_cl01`  
Just remember that children names should be unique in the hosts file.

<details><summary>Show Code</summary>

```yaml {linenos=inline,title=dvs_cc_cl01.yml}
all:
  children:
    codecrusaders: xx
      children:
        distributed_switches:
          children:
            dvs_cc_cl01:
              hosts:
                vc01_dvs01:
            dvs_cc_cl02:
              hosts:
                vc01_dvs02:
```

</details>

{{< highlight yaml "linenos=true, hl_lines=5-7">}}
all:
  children:
    codecrusaders: xx
      children:
        distributed_switches:
          children:
            dvs_cc_cl01:
              hosts:
                vc01_dvs01:
            dvs_cc_cl02:
              hosts:
                vc01_dvs02:
.
{{< /highlight>}}




With the hosts file in place I am ready to create two group_vars files. They need to be named after the children in the hosts file `dvs_cc_cl01.yml` and `dvs_cc_cl02.yml` and have to be placed in the `inventory\group_vars` folder. When hosts are defined under children and a group\_var file exists named after the children the hosts belongs to, ansible will know it has to use the variables defined in the group\_var file. Just try it out if you are not familiar with this concept.

In each file I will create a dict and this dict represents the desired configuration of the distributed virtual switch. I like to define all possible or possibly allowed portgroups for the distributed virtual switch and then toggle the absent/present switch to customize availability for portgroups on cluster/VDS level. This is the example for cl01, the cl02 file has the `pg_state` switch toggled to present.

<!---
```yaml {linenos=true title="some.bla"}
portgroups:
  'VLAN10':
    vlan_nr: 10
    num_ports: 16
    pg_type: earlyBinding
    pg_state: present
  'VLAN11':
    vlan_nr: 11
    num_ports: 16
    pg_type: earlyBinding
    pg_state: absent
  &#039;VLAN99&#039;:
    vlan_nr: 99
    num_ports: 16
    pg_type: earlyBinding
    pg_state: present
```
--->

Now that the hard work is done, the only thing left is create the task and using the <a rel="noreferrer noopener" href="https://docs.ansible.com/ansible/latest/modules/vmware_dvs_portgroup_module.html" target="_blank">vmware_dvs_portgroup</a> module.  
You will notice that in the pre-task there is a check for using the `-l` or `--limit` option when running the code. This is to make sure that you know what I am doing and not accidently run code against hosts that I don&#8217;t intend to. The variable `inventory_hostname` is calling the VDS in the hosts file. The variable `item` is calling defined variables in the group_var file. Where `item.key` is the portgroupname before naming convention and `item.value` holds the other variables belonging to the portgroup. With the `loop` command I iterate over the items.

<pre title="vsphere_dvs_portgroups.yml" class="wp-block-code"><code lang="yaml" class="language-yaml">---
- name: set or remove portgroups from dvs
  hosts: all
  connection: local

  # pre task to make sure you use the --limit ec2_vpc_dhcp_option
  pre-tasks:
    - name: check for use of limit
      fail:
        msg: "use -l or --limit to run"
      when: ansible_limit is not defined
      run_once: true

  # make the task iterate over dicts defined in group_var files
  tasks:
    - name: set or remove portgroup
      vmware_dvs_portgroup:
        hostname: vc01
        username: your_vcenter_username
        password: your_vcenter_password
        validate_certs: false
        portgroup_name: "{{ inventory_hostname }}-{{ item.key }}"
        switch_name: "{{ inventory_hostname }}"
        vland_id: "{{ item.value.vlan_nr }}"
        num_ports: "{{ item.value.vlan_nr }}"
        portgroup_type: "{{ item.value.pg_type }}"
        state: "{{ item.value.pg_state }}"
      loop: "{{ query(&#039;dict&#039;,portgroups) }}"
</code></pre>

now run the script with:

<pre class="wp-block-code"><code lang="bash" class="language-bash">$ ansible-playbook -i inventory/hosts playbooks/devops/vsphere_dvs_portgroups.yml --limit distributed_switches</code></pre>

#### Result

In vc01 you should now see the desired configuration on the two distributed virtual switches.  
**vc01_dvs01:**  
`vc01_dvs01-VLAN10<br>vc01_dvs01-VLAN99`  
**vc01_dvs02:**  
`vc01_dvs02-VLAN10<br>vc01_dvs02-VLAN11<br>vc01_dvs02-VLAN99`

This was my first &#8216;official&#8217; blogpost, thanks for reading and I hope it was useful to you. If you have any comments, tips or ideas. Please let me know!

ps.  
some of you might have noticed, I am using the terms VDS and DVS, respectively vSphere Distributed Switch and Distributed Virtual Switch, in an inconsistent way. This I have done on purpose because I can&#8217;t find the definite answer. Suggestions welcome. 

<!-- LikeBtn.com BEGIN --><span class="likebtn-wrapper" data-identifier="post\_4043" data-site\_id="5ede12216fd08b0113e4e22b" data-dislike\_enabled="false" data-icon\_dislike\_show="false" data-style="" data-unlike\_allowed="" data-show\_copyright="" data-item\_url="https://www.codecrusaders.nl/vmware/creating-uniform-portgroups-on-multiple-vsphere-distributed-switches-with-ansible/" data-item\_title="Creating uniform portgroups on vSphere Distributed Switch with Ansible" data-item\_image="https://www.codecrusaders.nl/wp-content/uploads/2020/06/Ansible-vmware\_01.png" data-item\_date="2020-06-05T15:36:17+00:00" data-engine="WordPress" data-plugin\_v="2.6.47" data-prx="https://www.codecrusaders.nl/wp-admin/admin-ajax.php?action=likebtn\_prx" data-event\_handler="likebtn\_eh" ></span>

<!-- LikeBtn.com END -->