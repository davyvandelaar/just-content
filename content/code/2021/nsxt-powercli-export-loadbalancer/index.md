---
title: Nsxt Powercli Export Loadbalancer
date: 2021-05-29T16:26:16+00:00
draft: false

categories:
  - code
  - devops
tags:
  - nsxt
  - powercli
  - powershell
  - vmware
toc: false
author: Davy
---

ow to export NSX-T loadbalancer information with PowerCLI? This question popped in my mind after a customer request to generate a list with their loadbalancers and associated virtual servers. This was not as easy as I thought! First of all, the NSX-T userinterface is not very export-friendly and i could not find a way to export data. That doesn&#8217;t matter though, because I am an automation kinda guy anyways. So I thought let&#8217;s take a look at PowerCLI for NSX-T. So I did work with PowerNSX before and I just assumed something similar would exist for NSX-T, right? Well, no. There is a module called `VMware.VimAutomation.Nsxt` and it has only four commands. So I dug into the world of NSX-T with PowerCLI and share my experiences with you.

#### How to make it work

This might sound like captain obvious, but you never know. Connect to NSX-T.

<pre class="wp-block-code"><code lang="powershell" class="language-powershell line-numbers">Connect-NsxtServer -Name &lt;em&gt;yournsxserver&lt;/em&gt; -Credential $creds</code></pre>

Once connected things get interesting. This modules talks directly to the API and you will need to find out which parts contain the information you need. Personally I try to keep things simple and have some trust in the naming conventions of VMware. It is important to know which information you want to retrieve and it helps if you have any idea what the &#8216;top-layer&#8217; is in the chain. Let me break this down with the original question in mind.

I want an overview of the available **loadbalancers** and connected **virtual servers**. So it makes sense that in this case my starting point would be the loadbalancer, right? Let&#8217;s give it a try. I am going to do a simple wildcard search on **loadbalancer**. This will produce a nice list.

<details><summary>Show Code: Get-NsxtService *loadbalancer*</summary>

```powershell {linenos=false}
 D:\scripts > Get-NsxtService *loadbalancer* 
 Name
 com.vmware.nsx.loadbalancer.application_profiles
 com.vmware.nsx.loadbalancer.client_ssl_profiles
 com.vmware.nsx.loadbalancer.monitors
 com.vmware.nsx.loadbalancer.node_usage_summary
 com.vmware.nsx.loadbalancer.persistence_profiles
 com.vmware.nsx.loadbalancer.pools
 com.vmware.nsx.loadbalancer.rules
 com.vmware.nsx.loadbalancer.server_ssl_profiles
 com.vmware.nsx.loadbalancer.services
 com.vmware.nsx.loadbalancer.services.debug_info
 com.vmware.nsx.loadbalancer.services.pools.statistics
 com.vmware.nsx.loadbalancer.services.pools.status
 com.vmware.nsx.loadbalancer.services.statistics
 com.vmware.nsx.loadbalancer.services.status
 com.vmware.nsx.loadbalancer.services.usage
 com.vmware.nsx.loadbalancer.services.virtual_servers.statistics
 com.vmware.nsx.loadbalancer.services.virtual_servers.status
 com.vmware.nsx.loadbalancer.ssl.ciphers_and_protocols
 com.vmware.nsx.loadbalancer.usage_per_node
 com.vmware.nsx.loadbalancer.virtual_servers
 com.vmware.nsx.repository.bundles.upload_allowed
 com.vmware.nsx.repository.bundles.upload_status
 com.vmware.nsx.upgrade.bundles.upload_status
```

 </details>
 <br>
 

#### Dive a little deeper

Nice! That looks like something I could use. Still, where to start? On this I am going to make an educated guess. I am looking for the loadbalancer service, so let&#8217;s go for **loadbalancer.services**. I want to know what is in there, so let&#8217;s do a list. To do this I will first turn the initial command into a variable and then do a list action. To dive into the information we need you will have to add **results.** So let&#8217;s do this. In the example I did alter the ID&#8217;s, so they are not real ID&#8217;s.

<pre class="wp-block-code"><code lang="powershell" class="language-powershell">PS D:\scripts&gt; $lb_info = Get-NsxtService com.vmware.nsx.loadbalancer.services
PS D:\scripts&gt; $lb_info.list().results
Help                   : @{Documentation=; links=; schema=; ..
 enabled                : True
 relax_scale_validation : False
 size                   : MEDIUM
 error_log_level        : INFO
 virtual_server_ids     : {abba11ab-11a1-2222-b3b3-1234567ab890, acca22ab-12a3-2244-c3b8-1244267ff890, etc}
 attachment             : @{Help=; target_id=0a0e1234-a2a6-87cc-2a30-1234567b22f; target_display_name=T1-CCDV-LB01; target_type=LogicalRouter; is_valid=True}
 resource_type          : LbService
 id                     : 1234b5c2-2345-6789-1fa2-1122da3cdabc
 display_name           : CC-LB01
 tags                   : {@{Help=; scope=policyPath; tag=/infra/lb-services/CC-LB01}}
 create_user            : nsx_policy
 create_time            : 1608738181111
 last_modified_user     : nsx_policy
 last_modified_time     : 1621521661111
 system_owned           : False
 protection             : REQUIRE_OVERRIDE
 revision               : 72</code></pre>

In my case there was more than one loadbalancer. So the loadbalancer **display_name** is something I definitely can retrieve from here with a simple **foreach** loop. However, this won&#8217;t give you the connected virtual servers. The only thing we can retriever here are the **virtual\_server\_ids**. So let&#8217;s do some further testing. I think I want a list of virtual server id&#8217;s per loadbalancer. How to do this? For this you will need to use the **get()** command and use the id of the desired loadbalancer between the brickets. How do I know it has to be the id? I use a simple trick, just by making the command fail and read the output. Like this.

<pre class="wp-block-code"><code lang="powershell" class="language-powershell">PS D:\scripts&gt; $lb_info.get()
 The path variable {service-id} in the url has not been resolved.
&lt;&lt;em&gt;now use the id&lt;/em&gt; from above&gt; 
PS D:\scripts&gt; $lb_info.get(&#039;1234b5c2-2345-6789-1fa2-1122da3cdabc&#039;).display_name
&lt;em&gt;&lt;will display the name of the loadbalancer&lt;/em&gt;&gt;
PS D:\scripts&gt; $lb_info.get(&#039;1234b5c2-2345-6789-1fa2-1122da3cdabc&#039;).virtual_server_ids
&lt;&lt;em&gt;will give a list of virtual servers belonging to the loadbalancer&lt;/em&gt;&gt;</code></pre>

The same trick applies to virtual servers. Take a look in the wildcard list above and find **com.vmware.nsx.loadbalancer.virtual_servers**. Turn this into into a variable, **$vs_info** for instance. And then try to retrieve a display name from any of the **virtual\_server\_ids** you found earlier. It would look like this.

<pre class="wp-block-code"><code lang="powershell" class="language-powershell">PS D:\scripts&gt; $vs_info = Get-NsxtService com.vmware.nsx.loadbalancer.virtual_servers
PS D:\scripts&gt; $vs_info.get(&#039;abba11ab-11a1-2222-b3b3-1234567ab89&#039;)
Help                       : @{Documentation=; links=; 
 enabled                    : True
 access_log_enabled         : False
 ip_address                 : 12.12.123.12
 port                       : 80
 ports                      : {80}
 default_pool_member_port   : 8006
 default_pool_member_ports  : {8006}
 ip_protocol                : TCP
 pool_id                    : &lt;a pool id number&gt;
 application_profile_id     : &lt;an application profile number&gt;
 client_ssl_profile_binding : @{Help=; ssl_profile_id= 
 log_significant_event_only : False
 resource_type              : LbVirtualServer
 id                         : 1234b5c2-2345-6789-1fa2-1122da3cdabc
 display_name               : codecrusaders_vip
 tags                       : {@{Help=;}}
</code></pre>

#### How to turn it into a working PowerCLI script

Now, how do you stitch all this together? Well, this is actually quite simple. Once you know how to retrieve information from the id&#8217;s, possibilities are endless. I decided that I want an overview of each Virtual Server per loadbalancer. When applicable each server has to list: Virtual Server, Virtual Server IP, Virtual Server port, Virtual Server Default Pool Member Port, Pool Name, Application Profile ID, SSL Profile ID, Certificate, Virtual Server Enabled. Then I want to export this to a CSV. 

This seems quite a list, the code to do this however is remarkably short. Let&#8217;s see what I did. 

<pre title="nsxt-loadbalancer-export" class="wp-block-code"><code lang="powershell" class="language-powershell line-numbers"># Connect to NSX-T
Connect-NsxtServer -Name &lt;em&gt;yournsxserver&lt;/em&gt; -Credential $creds

# get lists of Load Balancers and Virtual Servers
$lb_svc = Get-NsxtService com.vmware.nsx.loadbalancer.services
$lb_vs = Get-NsxtService com.vmware.nsx.loadbalancer.virtual_servers 
$lb_pools = Get-NsxtService com.vmware.nsx.loadbalancer.pools
$ssl_profiles = Get-NsxtService com.vmware.nsx.loadbalancer.client_ssl_profiles
$certificates = Get-NsxtService com.vmware.nsx.trust_management.certificates
$app_profiles = Get-NsxtService com.vmware.nsx.loadbalancer.application_profiles

# retrieve a list of Load Balancer ID&#039;s
$lb_ids = $lb_svc.list().results.id

foreach ($lb_id in $lb_ids) {
    $lb_vs_ids = $lb_svc.get("$lb_id").virtual_server_ids   # retrieve list of virtual machine ID&#039;s per LB
    foreach ($vs_id in $lb_vs_ids) {
        $lb_vs.get("$vs_id") | Select-Object @{N=&#039;Load Balancer&#039;;E={$lb_svc.get("$lb_id").display_name}},
        @{N=&#039;Virtual Server&#039;;E={$_.display_name}}, # Search virtual machine ID&#039;s and match them with a name + create table
        @{N=&#039;Virtual Serper IP&#039;;E={$_.ip_address}},
        @{N=&#039;Virtual Server port&#039;;E={$_.ports}},
        @{N=&#039;Virtual Server Default Pool Member Port&#039;;E={$_.default_pool_member_ports}},
        @{N=&#039;Pool Name&#039;;E={$lb_pools.get($_.pool_id).display_name}},
        @{N=&#039;Application Profile ID&#039;;E={$app_profiles.get($_.application_profile_id).display_name}},
        @{N=&#039;SSL Profile ID&#039;;E={$ssl_profiles.get($_.client_ssl_profile_binding.ssl_profile_id).display_name}},
        @{N=&#039;Certificate&#039;;E={$certificates.get($_.client_ssl_profile_binding.default_certificate_id).display_name}},
        @{N=&#039;Virtual Server Enabled&#039;;E={$_.enabled}} | Export-Csv .\output\lb_report.csv -NoTypeInformation -UseCulture -Append
    }
}</code></pre>

This wil create a CSV file with all the information I need. For readability to my customer I then can open it in Excel and turn the data into a good looking table with headers and search option. Pretty cool! 

Ofcourse there are many options possible and routes to take. This code is focussed on the loadbalancer, but it doesn&#8217;t have to be just that. It&#8217;s even possible to create, I recommend reading the the examples that come with the module commands. 

#### Conclusion

In my <a href="https://www.codecrusaders.nl/vmware/tanzu/kubernetes-and-cloud-native-as-part-of-my-new-job/" target="_blank" rel="noreferrer noopener">previous blog post</a> I promised to write something technical about kubernetes, don&#8217;t worrry, that is still in the pipeline! This however came in between. I couldn&#8217;t find a whole lot on this on the internet and since I had good fun playing around with it I decided to make it a post. It&#8217;s a fun way to discover NSX-T with PowerCLIand I can recommend trying this out. Thanks for reading!
