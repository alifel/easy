# Inside look at modern web browser (part 1) 

## CPU, GPU, Memory, and multi-process architecture

In this 4-part blog series, we'll look inside the Chrome browser from high-level architecture to the specifics of the rendering pipeline. If you ever wondered how the browser turns your code into a functional website, or you are unsure why a specific technique is suggested for performance improvements, this series is for you.

As part 1 of this series, we'll take a look at core computing terminology and Chrome's multi-process architecture.

Note: If you are familiar with the idea of CPU/GPU and process/thread you may skip to [Browser Architecture](https://developer.chrome.com/blog/inside-browser-part1#browser-architecture).

## At the core of the computer are the CPU and GPU

To understand the environment that the browser is running, we need to understand a few computer parts and what they do.

### CPU

![CPU](./inside-look-at-modern-web-browser(part1)-images/cpu-fc96f4ee3715f_1920.png)

Figure 1: 4 CPU cores as office workers sitting at each desk handling tasks as they come in

First is the Central Processing Unit - or CPU. The CPU can be considered your computer's brain. A CPU core, pictured here as an office worker, can handle many different tasks one by one as they come in. It can handle everything from math to art while knowing how to reply to a customer call. In the past most CPUs were a single chip. A core is like another CPU living in the same chip. In modern hardware, you often get more than one core, giving more computing power to your phones and laptops.

### GPU

![GPU](./inside-look-at-modern-web-browser(part1)-images/gpu-ff4b6081ab9b1_1920.png)

Figure 2: Many GPU cores with wrench suggesting they handle a limited task

Graphics Processing Unit - or GPU is another part of the computer. Unlike CPU, GPU is good at handling simple tasks but across multiple cores at the same time. As the name suggests, it was first developed to handle graphics. This is why in the context of graphics "using GPU" or "GPU-backed" is associated with fast rendering and smooth interaction. In recent years, with GPU-accelerated computing, more and more computation is becoming possible on GPU alone.

When you start an application on your computer or phone, the CPU and GPU are the ones powering the application. Usually, applications run on the CPU and GPU using mechanisms provided by the Operating System.

![layers](./inside-look-at-modern-web-browser(part1)-images/hardware-os-application-d4c78524d5558_1920.png)

Figure 3: Three layers of computer architecture. Machine Hardware at the bottom, Operating System in the middle, and Application on top.

## Execute program on Process and Thread

![process](./inside-look-at-modern-web-browser(part1)-images/process-threads-6fe2ce3d7b3e2_1920.png)

Figure 4: Process as a bounding box, threads as abstract fish swimming inside of a process

Another concept to grasp before diving into browser architecture is Process and Thread. A process can be described as an application's executing program. A thread is the one that lives inside of process and executes any part of its process's program.

When you start an application, a process is created. The program might create thread(s) to help it do work, but that's optional. The Operating System gives the process a "slab" of memory to work with and all application state is kept in that private memory space. When you close the application, the process also goes away and the Operating System frees up the memory.

![diagram of process](./inside-look-at-modern-web-browser(part1)-images/process-memory-60ff42694f726.svg)

Figure 5: Diagram of a process using memory space and storing application data

A process can ask the Operating System to start another process to run different tasks. When this happens, different parts of the memory are allocated for the new process. If two processes need to talk, they can do so by using Inter Process Communication (IPC). Many applications are designed to work this way so that if a worker process get unresponsive, it can be restarted without stopping other processes which are running different parts of the application.

![diagram of separate](./inside-look-at-modern-web-browser(part1)-images/worker-process-ipc-ea8392115a438.svg)

Figure 6: Diagram of separate processes communicating over IPC

## Browser architecture

So how is a web browser built using processes and threads? Well, it could be one process with many different threads or many different processes with a few threads communicating over IPC.

![different](./inside-look-at-modern-web-browser(part1)-images/browser-architecture-9d143004c2a63_1920.png)

Figure 7: Different browser architectures in process / thread diagram

The important thing to note here is that these different architectures are implementation details. There is no standard specification on how one might build a web browser. One browser's approach may be completely different from another.

For the sake of this blog series, we're using Chrome's recent architecture, described in Figure 8.

At the top is the browser process coordinating with other processes that take care of different parts of the application. For the renderer process, multiple processes are created and assigned to each tab. Until very recently, Chrome gave each tab a process when it could; now it tries to give each site its own process, including iframes (see [Site Isolation](https://developer.chrome.com/blog/inside-browser-part1#site-isolation)).

![renderer](./inside-look-at-modern-web-browser(part1)-images/browser-architecture-998609758999a_1920.png)

Figure 8: Diagram of Chrome's multi-process architecture. Multiple layers are shown under Renderer Process to represent Chrome running multiple Renderer Processes for each tab.

## Which process controls what?

The following table describes each Chrome process and what it controls:

### Process and What it controls

#### Browser

Controls "chrome" part of the application including address bar, bookmarks, back and forward buttons.
Also handles the invisible, privileged parts of a web browser such as network requests and file access.

#### Renderer

Controls anything inside of the tab where a website is displayed.

#### Plugin

Controls any plugins used by the website, for example, flash.

#### GPU

Handles GPU tasks in isolation from other processes. It is separated into different process because GPUs handles requests from multiple apps and draw them in the same surface.

![diffrent](./inside-look-at-modern-web-browser(part1)-images/chrome-processes-79aaecca78d23_1920.png)

Figure 9: Different processes pointing to different parts of browser UI

There are even more processes like the Extension process and utility processes. If you want to see how many processes are running in your Chrome, click the options menu icon (3个竖的点) at the top right corner, select More Tools, then select Task Manager. This opens up a window with a list of processes that are currently running and how much CPU/Memory they are using.

## The benefit of multi-process architecture in Chrome

Earlier, I mentioned Chrome uses multiple renderer process. In the most simple case, you can imagine each tab has its own renderer process. Let's say you have 3 tabs open and each tab is run by an independent renderer process.

If one tab becomes unresponsive, then you can close the unresponsive tab and move on while keeping other tabs alive. If all tabs are running on one process, when one tab becomes unresponsive, all the tabs are unresponsive. That's sad.

![diagram](./inside-look-at-modern-web-browser(part1)-images/multiple-renderer-tabs-c29a1fd34d4d_1920.png)

Figure 10: Diagram showing multiple processes running each tab

Another benefit of separating the browser's work into multiple processes is security and sandboxing. Since operating systems provide a way to restrict processes' privileges, the browser can sandbox certain processes from certain features. For example, the Chrome browser restricts arbitrary file access for processes that handle arbitrary user input like the renderer process.

Because processes have their own private memory space, they often contain copies of common infrastructure (like V8 which is a Chrome's JavaScript engine). This means more memory usage as they can't be shared the way they would be if they were threads inside the same process. In order to save memory, Chrome puts a limit on how many processes it can spin up. The limit varies depending on how much memory and CPU power your device has, but when Chrome hits the limit, it starts to run multiple tabs from the same site in one process.

## Saving more memory - Servicification in Chrome

The same approach is applied to the browser process. Chrome is undergoing architecture changes to run each part of the browser program as a service allowing to split into different processes or aggregate into one.

General idea is that when Chrome is running on powerful hardware, it may split each service into different processes giving more stability, but if it is on a resource-constraint device, Chrome consolidates services into one process saving memory footprint. Similar approach of consolidating processes for less memory usage have been used on platform like Android before this change.

![diagram](./inside-look-at-modern-web-browser(part1)-images/chrome-servification-f06f547c54405.svg)

Figure 11: Diagram of Chrome's servicification moving different services into multiple processes and a single browser process

## Per-frame renderer processes - Site Isolation

[Site Isolation](https://developers.google.com/web/updates/2018/07/site-isolation) is a recently introduced feature in Chrome that runs a separate renderer process for each cross-site iframe. We've been talking about one renderer process per tab model which allowed cross-site iframes to run in a single renderer process with sharing memory space between different sites. Running a.com and b.com in the same renderer process might seem okay. The [Same Origin Policy](https://developer.mozilla.org/docs/Web/Security/Same-origin_policy) is the core security model of the web; it makes sure one site cannot access data from other sites without consent. Bypassing this policy is a primary goal of security attacks. Process isolation is the most effective way to separate sites. With [Meltdown and Spectre](https://developers.google.com/web/updates/2018/02/meltdown-spectre), it became even more apparent that we need to separate sites using processes. With Site Isolation enabled on desktop by default since Chrome 67, each cross-site iframe in a tab gets a separate renderer process.

![p12](./inside-look-at-modern-web-browser(part1)-images/site-isolation-2521dc96bb823_1920.png)

Figure 12: Diagram of site isolation; multiple renderer processes pointing to iframes within a site

Enabling Site Isolation has been a multi-year engineering effort. Site Isolation isn't as simple as assigning different renderer processes; it fundamentally changes the way iframes talk to each other. Opening devtools on a page with iframes running on different processes means devtools had to implement behind-the-scenes work to make it appear seamless. Even running a simple Ctrl+F to find a word in a page means searching across different renderer processes. You can see the reason why browser engineers talk about the release of Site Isolation as a major milestone!

## Wrap-up

In this post, we've covered a high-level view of browser architecture and covered the benefits of a multi-process architecture. We also covered Servicification and Site Isolation in Chrome that is deeply related to multi-process architecture. In the next post, we'll start diving into what happens between these processes and threads in order to display a website.

Did you enjoy the post? If you have any questions or suggestions for the future post, I'd love to hear from you at [@kosamari](https://twitter.com/kosamari) on Twitter.
