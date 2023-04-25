---
title: "Navigating Kubecon 2023 Like a Newbie"
date: 2023-04-25T17:27:31+02:00
draft: false

imageSlider: true   #set to false if you don't want to use the imageSlider but a featuredImage

categories:
  - article
tags:
  - cloudnative
  - kubernetes
  - linuxfoundation
  - devops
toc: true
author: Davy
---
This year the KubeCon + CloudNativeCon 2023 event took place in the RAI Amsterdam. With 10k+ tickets sold the event was completely sold out. For me and for over 5000 (!) other attendees this was the first in person visit to KubeCon + CloudNativeCon ever. To me this underlines the importance of cloudnative and it's eco-system. Also, enough material and impressions to write an article on 'Navigating Kubecon 2023 Like a Newbie'. It's a long read, but hopefully you like to join me on my journey.

## ITQ - Team
First and above all I want to mention the truly fabulous group of ITQ collegues that joined forces this year. If I am not mistaken, ITQ was present with at least 12 people. Three 'veterans' and nine first timers. As far as I can tell, we all had a blast. Also, Robert Kloosterhuis and Carlos Mestre Del Pino had some very interesting live-interviews. Sometimes with an unexpected twist. Take a look.

### ITQ - Live interviews
There are many interviews taken. I will pick a few out, feel free to watch them all.
- [Live Interview with Michael Cote](https://www.youtube.com/live/87IGAxHzAe8?feature=share) - beware the band-interruption!
- [Live Interview with Steve Wong](https://www.youtube.com/live/WE-b7UEfF6w?feature=share)
- [Live Interview with Dan Garfield](https://www.youtube.com/live/qqupYQTstuQ?feature=share)

### ITQ - vBeers @ Batavia 1920
This event took place on Tuesday, April 18th. After a first full day of KubeCon + CloudNativeCon. Originally this event has it's roots in the VMware community with strong ties to the VMware eco-system. Ofcourse, we have strong ties with VMware and the Modern Apps portfolio. Especially Tanzu with all the tools involved. However, we were also curious if we could interest people outside of this eco-system for this event. The answer is yes! We had a turnout of about 60 people with many different companies and roles involved. There was a good and vibrant atmosphere. Some good old mingling and networking took place and as far as I could tell everybody was happy. Also, very cool to meet so many new peers. I believe that these events are important. For getting to know each other, but also to get a better understanding of each other. A big thank you to everyone who made this to the success it was!

## Navigating Kubecon 2023 Like a Newbie
This event was in many-many ways super cool, but also overwhelming. So, I will summarize my experiences day by day. Taking you on my personal journey. Along the way I will point out the highlights, thoughts, things that could have been better and lessons learned.

### Day 0 - Badge Pickup and dinner
Monday, April 17th. While this not being an actual KubeCon + CloudeNativeCon day, I do want to mention it. Especially because on this day you could already feel the good vibes of what was going to come.

#### Badge Pickup
For starters, it was a nice walk from the hotel to the RAI. In about 30 minutes we arrived at the event location and I must say, badge pickup was super easy. No lines, fully automated and printed for you. The hardest part was probably to get the paper it in the plastic holder. It is a good idea to do badge pickup prior to the start of the event. This will save you a considerablle amount of waiting time. 

#### ITQ pre-event dinner
To kick-off the event an amazing dinner was organized at [Holi Restaurant](https://holiamsterdam.nl/). We were honored to have Leigh Tuttle (with her husband Bobby) and David Palilonis from the Linux Foundation with us. The food and atmosphere were great and some great conversations were going on. My impression is that Leigh and David were quite impressed with the all-over good vibes they got from ITQ. 

### Day 1 - Co-hosted events
Tuesday, April 18th. Finally, the first 'real' event day was there. With an all-access pass it was possible to visit all the CNCF co-hosted events. A co-hosted CNCF event is a mini event focussed on one theme. The co-hosted events this year were either half-day or full-day events and all took place int the RAI event location. In case you didn't have an all-access pass there were still lightning talks, project meetings and off-site co-sponsor events to attend on the first day.

#### ArgoCon
Argo is booming. Ofcourse, I choose this event because I hear so many people talk about Argo and especially [ArgoCD](https://argo-cd.readthedocs.io/en/stable/). So, I felt like this was something I should attend. Well, I was not alone! Full house during the key-note and both ballrooms were full or near full all day. My problem as an Argo noob though was context. It sort of is assumed that everybody in the room has some Argo basic knowledge. Which I don't. So, I picked up some stuff on Canary and Blue+Green deployments, although due to lacking context other stuff remained a little too abstract. 

#### Kubernetes Batch + HPC Day
During lunch I was in doubt. Do I go on with ArgoCD, or am I going to shift focus. After having some doubts I decided to go sit down at Kubernetes Batch + HPC day. This has a focus on AI en ML and I find this super interesting. This turned out to be a really interesting choice. Especially the talk called: '[Sharing is Caring - Fractional GPU with MetaGPU K8s Device Plugin](https://youtu.be/bG8ZSzucJXo)'. This was a demo of the [MetaGPU](https://github.com/cnvrg/metagpu) K8s device plugin. 

This project is kubernetes native and focusses on sharing and scaling K8s on GPU's. Because of all the differences in GPU sizes and capabilities the plugin tries to abstract this layer and deliver unified access and capabilities to users. By dividing each GPU in 100 pieces, representing 100%, it becomes highly scalable with zero downtime. The plugin also provides more insight for non-root users into what is happening on the GPU. As AI and ML become increasingly more important I believe it essential to utilize the power of K8s on GPU.

### Day 2 - What the -bleep- is happening!
Wednesday, April 19th. First 'all-attendee' day at KubeCon + CloudNativeCon 2023. Over 10000 people, that is a lot! So many people, so many talks, so much of everything. How did I navigate through this day?

#### Key-Note
This was massive. For me personally, a bit underwhelming though. Ofcourse, I know how keynotes go and how orchestrated this is. Unfortunately it couldn't really catch me. One thing I do say, I really like the diversity and how inclusive the event is. For me that is a big plus! 

#### Talk: The Power of Self Managing Clusters
This was a very interesting presentation about abstracting the need for K8s management cluster in a multitenant environment. The cluster would become self-managing and self-healing. At some point I remember thinking, "why don't we all do this?". The answer to that is, you will need vCloud Director. There is always a catch, right? For those who are involved with vCloud Director, you should check this out!

#### Solution Exchange
This is where my head took a spin! It is like a million lights start to blink and you want to go to them all at once at the same time. Ofcourse, I picked up stickers, swag and listened to some of the people at the booths. Mostly I was looking for solutions and software that brings some CLI backend stuff more to the foreground. The one that got my attention was Lens. Also, to be honest, I need to look further into this. 

#### Talk: Choose Your Own Adventure
Highlight of the day! Victor Farcic on his laptop and Whitny Lee as MC. The goal was to build a really simple application from scratch. With a live demo where the audience was in control of the tools to use for Victor. This was really fun! Also, along the way it becomes clear that there are many ways to your application. You need to think about your choices. They also do this on [Youtube](https://www.youtube.com/@DevOpsToolkit). A must watch!

#### Talk: How to blow up a Kubernetes Cluster
The room was packed way and beyond! Too many individuals interested in blowing up stuff. Despite the catchy title, this was all about resource management. Requests and limits. Especially the danger of setting limits on nodes. For me this triggered a lot of 'old' VMware vSphere knowledge. Problems that now start to rise in the Kubernetes and CloudNative world because products are being more widely used in all kind of environments.

### Day 3 - Let's go structured!
Thursday, April 20th. So, after a very full and eventfull Wednesday I decided to take a step back. First of all, I made sure to have a good night sleep. This was truly the best decision of the day. So, that's my tip for next year newbies. **Get enough sleep!** Because day 2 was so hectic I decided to tone down a little bit. My plan was simple, hang around the breakout rooms and just attend some sessions. Whatever is coming, I'll take it. Out of four I am highlighting two.

#### Talk: Kubernetes Database Operators Landscape
This was a panel discussion. Usually I am not to keen on these discussions in large rooms. However, this one was interesting. Because databases and data on Kubernetes becomes more important ewvery day. There is a need and search for standarization. This is why the DoK community was founded. DoK stands for Data on Kubernetes. If you need to work with data on k8s or design a solution, you should visit their site [here](https://dok.community/). And probably the [OperatorHub.io](https://operatorhub.io/?category=Database) is an interesting place as well. 

#### Talk: Use Knative when you can, and Kubernetes when you must
Knative us an orchestration platform for Native Kubernetes Services. Where native is a Microservice, a serverless container or a serverless function. It automates the entire microservice deployment process and users only need to define high level service definition. Deep Kubernetes knowledge is not required. To be as efficient as it is, the stack is highly opinionated and comes with built in security. The Guard security is especially interesting because it can detect anomalies and it will destroy any pod with such behaviour. As user you will not notice, but a possible attacker lost all it's work. Pretty cool! Find out more on their [site](https://knative.dev/docs/).

### Day 4 - Closing day
Friday April 21st. This is the last day of the event. In the schedule I found two tutorials and decided to go with them. 

#### Tutorial: Create and Deploy a Lightweight Microservice in WebAssembly
So, I was very curious to WebAssembly (WASM) and what it could do. The presentation that came with the tutorial was one of those where lots of assumptions were made. It is kind of assumed that you know about Rust, Databases, etcetera. The presenter was also the founder of the project and I assume that he thought he used very easy toned down language. This was not the case, at least for me. I tried to do the demo, but got stuck in troubleshooting MySql. However, once up and running I think it is pretty cool to work with Microservices and save a lot of unneccesary space. [Here](https://github.com/second-state/kubecon-eu-2023/blob/main/02-anatomy-wasm.md) is the demo and [here](https://webassembly.org/) is the Webassembly site. 

#### Tutorial: Building an Open Source Observability stack
This ended up being more of a demo than a tutorial, but that was fine. Because it was really good. They gave a good insight in what is called the FOP-stack (FluentBit, OpenTelemetry and Prometheus) and how it all works together. How you can gather taces, logs, metrics and ananlyze it. Finally Pixie is added to make it a FOPP stack, where Pixie provides Kubernetes observability for developers. I thought this was very interesting and definitely worth looking further into. Read more [Pixie](https://px.dev/), [FluentBit](https://fluentbit.io/), [OpenTelemetry](https://opentelemetry.io/) and [Prometheus](https://prometheus.io/). Also, if you want to do the demo, look [here](https://github.com/pixie-labs/opentelemetry-helm-charts). 

## Conclusion
This was an awesome event. It can be a bit overwhelming, especially when you are new. However, it's worth it. This article only scratches to all the impressions I got and the ideas I came back with. I leave it with that.

Thanks for reading.
