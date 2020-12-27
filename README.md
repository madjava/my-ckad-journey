My CKAD Journey - Exam Tips & Resources
===

It was a nice experience preparing for the **CKAD** exams and wanted to share my journey with others preparing for the same.

> Passing the K8s certification it all about practice, practice with more practicing. You have to know how it's done and be able to do it too.

## Table of contents

   * [Some K8s commands to know](#some-k8s-commands-to-know)
   * [Exam tips and tricks](#exam-tips-and-tricks)
   * [Training resources](#training-resources)
   * [CKAD Practice Tests](#ckad-practice-tests)
   * [Vim Guides](#vim-guide)
   * [Other materials](#other-materials)

### Some K8s commands to know

I had to get familiar with a few `kubectl` commands some of which turned out to be very useful knowledge when i actually sat for the exams. Follow the link below to find a compilation of a few and i hope would be useful to you too while practicing and on the main exam. You can find [some useful terminal commands for the CKAD exams here](kubectl-commands.md).

### Exam tips and tricks

- Set an alias for `kubectl` command immediately you start
  ```bash
  alias k=kubectl
  ```
  Then use just `k` in the terminal. e.g. `k get po`

- Know the abbreviated variants of some the K8s objects and resources. e.g
  - `po`  for `pods`
  - `svc` for `service`
  - `deploy` for `deployments`
  - `netpol` for `networkpolicy`
  - `pv` for `persistentvolume`
  - `pvc` for `persistentvolumeclaim`
  - `rs` for `replicaset`
  - `cm` for `configmap`

  This is not an exhaustive list though but found these to be common ones and can save you some typing, time and reduce typos too

- Learn to use the `-h` help flag for quick info on imperative commands if you are stuck or not entirely sure at that moment or want to quickly verify command options available. e.g.
  - `k run -h`
  - `k expose -h`
  - `k create -h`

  Lets assume you are asked to create a pod called xxx with xxx arguments and xxx labels with port xxxx. You could either:

  - Jump to the K8s documentation and get the info you need
  - Use `--dry-run-client` to create quick manifest file to build upon or
  - Quickly run `k run -h` which would list out example commands you can run, of which you can copy-paste and provide the required arguments then execute.


- Learn to use `explain`. It could come in handy if you are expected to provide certain properties in your manifest file and the example you are referencing on the K8s documentation does not include it. For example,
  - `k explain po.spec` or
  - `k explain po.spec.containers`

  While practicing learn to use and drill down using `explain` quickly. You can combine it (pipe it) with `less` or `grep` for even more granularity.

  `k explain po.spec --recursive | less`<br>
  `k explain po.spec.containers --recursive | grep -i readinessprobe -A15`

  This can be a handy tool if you find getting info from the documentation is beginning to become a hassle, have a play with various combinations and have your own system of digging around the manual from the terminal quickly.

- Learn to use `--dry-run=client -o yaml` to generate yaml manifest files with you can further edit. Also dont forget to redirect to an actual file e.g. `k run po nginx --dry-run=client -o yaml > my-pod.yaml` to create the manifest file.<br>

- 🧨 Don't create yaml files from scratch, you would run out of time for sure if you do that. You either copy an example from the documentation and modify or use the dry-run option to create a skeleton which is then edited accordingly.

- Learn to use the K8s documentation, have your bookmarks ready for speedy access. I have provided my bookmark [here](my-k8s-bookmarks.html.zip) that you can use for a start and build upon. Import that in your **Chrome browser** and further update.

- 🧨 Don't waist time on a question, if it's taking too long, flag it and comeback to it.

- Learn to list all pod resources in all namespaces for troubleshooting purposes. If you are ever in a situation where you have to find the falling pod and no further details as to where it is. As simple as this sounds if you don't have quick commands at hand you'll spend too much time drilling down namespaces and pods. Have a look at [my list of handy K8s commands](kubectl-commands.md) for some ideas.

- Learn to `ssh` into other nodes and execute K8s commands there and back out again. This helps speed up things in troubleshooting scenarios. So get familiar with jumping from node to node or examining pods and pod logs via the `exec` and `logs` command, some example can be found in the **Pod** section [here](kubectl-commands.md).

- Have some basic shell knowledge at hand like writing to a file at a particular location etc. A quick `cat` here and there can save you some seconds, `grep` can be very handy also.

- Get familiar with your Vim or Nano. CKAD is a very hands-on exam and you would need to use a terminal. No multiple choice questions. If you see multiple choice questions please pause and verify you are actually sitting for the CKAD 😝.

- Some tasks can be accomplished entirely using imperative commands, if you see such questions i advice you use the imperative option than looking for documentation or editing yaml files. e.g one quick imperative command usage is if required to create a `ConfigMap` or `Secret`, expose a `Pod` or scale up a deployment's replicas, you can accomplish these entirely from the command line.<br><br>The K8s documentation is packed with lots of very useful examples but for exam purposes if you know an imperative way of addressing a task use it. Have a look at some imperative commands i have complied [here](kubectl-commands.md).

- 🧨 Make sure you are in the right context, there is a command provided in the question section that would put you in the right place, be sure to execute it before you start any question, at least that's what i did, didn't take any chances 🤓

- 🧨 Be namespace aware, learn to execute tasks with the `-n` or `--namespace=<the namespace>` option when its required else your hard work would be in the wrong place. Use the short form preferably.

- 🧨 When you copy yaml examples from the K8s documentation, be sure to remove what's not needed and make sure you have edited what needs to be edited, e.g. the name of the pod, container names and image, pod labels, port numbers etc. that you may have carried over and which are not relevant to the task at hand.<br><br>If using Vim, practice deleting lines quickly, undoing changes and jumping to the top of the file after you paste snippets in. Also note formatting and indentation as it may go out of place after deleting or copy-pasting stuff from the documentation web page.

- Generally tasks around \'*Services & Networking*\', \'*State Persistence*\' and \'*Multi-Container Pods*\' tend to take longer time to complete. Not because of complexity but because there's lots of moving parts.<br><br>For example, to mount a volume on a pod you have to first create the `pv` then the `pvc` then the `pod` which has to be mounted on a volume, now imagine it's a multi-pod scenario then there even more to do.<br><br>Summary, practice doing these task quickly, for persistent volumes there are no imperative commands as at the moment to quickly create template manifest files to build upon, you have to go to the documentation. Also if you need to set up ingres or egress amongst services you have to reference the documentation.

 Learn to manage time in such scenarios, have your bookmark ready also. You can find what my Chrome bookmark looked like [here](my-k8s-bookmarks.html.zip)

- Don't forget to do a wee before you start, it's 2hrs long 😉. You'd be allowed to take a quick wee break but that's a few minutes off your time as the clock does not stop for you.

- Arrive early (start the exam early), 15min before. The verification process takes some time.

- Save yourself the hassles and close all running applications, if on a Mac run `cmd+alt+esc` to see what's still active in the background, leave only your browser app running.

- Make sure everywhere around you is clear, no books, papers or any other **_suspicious_** items. They are strict, but really nice and polite too 👌🏾

- You can have only one other tab open, let that be the K8s documentation page.

- If you don't have a webcam, best get one as you may have to do a `360deg` pan around where you are writing the exam and your desk space also. A webcam with a cord seems better suited, might be wonky panning with your PC but if that's what you've got or want to use it's fine also.

- Relax but be aware of time. Don't forget to attempt all questions even if not entirely sure of your answer, better a little than nothing at all.

- Remember to switch off your mobile, it's just needless distraction. I promise you'd survive 2hrs without it 🤓

- You need to wait a little over a day for your results. For me it was nerve racking and if you write yours on a Friday like i did then there would be no "day job" distractions to take your mind off the wait the whole of Saturday.

- If you don't make the first try, then i'd recommend you register to resit it earlier than later, so you don't begin to lose all that juicy perp-knowledge gained already but build on what you already know. Take a three days study break and plunge right back in 💪🏾.

From my own experience, if you have and gone through the exam scope, the [CKAD's Domain Competencies](https://training.linuxfoundation.org/certification/certified-kubernetes-application-developer-ckad/) topics and practiced lots of prep-questions there wouldn't be any "surprises" in the questions you'd see. The only thing left would be to manage your time and make sure you understand the requirement, don't assume, be sure and also know how to move quickly to access resource and info from the K8s documentation or manuals via the terminal to tackle the task at hand.

19 questions for 2 hours doesn't seems like alot but trust me the time tends to fly past so quickly, and the fact that you have lots of documentation options you can use such as the K8s documentation or the manuals via the `explain` command can be a blessing or a hinderance, you have to manage that properly. Have a very organised bookmark to save time, be familiar with how to get the info your need via `explain` command of the `-h` option for `run`, `create` and `expose` commands. I have offered my Chrome K8s bookmark [here](my-k8s-bookmarks.html.zip), this you can find this useful or a starting point in building yours.

Don't forget to flag it and circle back iteratively if a question is eating too much of your time or you don't yet understand what's required, that strategy helped me alot.

Honestly i didn't answer all the questions, i had a question left and it was obvious i had ran out of time a minute before my clock ran out i applied the manifest file i was working on, it ran successfully, i knew it was half baked but also knew that at least some requirements where in there.

Overall i was still happy i had managed my time well as i was able to get to all the questions, i guess the other 18 where in order or at least for the most parts to give me a passing score 🙂.


### Training resources

🔥 [Kubernetes Certified Application Developer (CKAD) with Tests](https://www.udemy.com/course/certified-kubernetes-application-developer/) by [Mumshad Mannambeth](https://www.udemy.com/user/mumshad-mannambeth/) and [Vijin Palazhi](https://www.udemy.com/user/vijin-palazhi-2/).

I got this via [Udemy](https://www.udemy.com/) and can't emphasise enough, this course is a **MUST HAVE** trust me on this one. The [KodeCloud](https://kodekloud.com) labs are just awesome and gives you first hand practice test and environment to work with. I went though the Lighting labs and Mock exams at least twice and also went through each and every practice test after each lecture sections and their solution videos to learn what i did wrong and also see how someone else would tackle the same task for the ones i got right.

👌🏾 [Kubernetes Made Easy: Learn Kubernetes From Scratch](https://www.udemy.com/course/kubernetes-made-easy/) by [Srinath Challa](https://www.udemy.com/user/srinathchalla/) via [Udemy](https://www.udemy.com/).

Srinath's explanations are just so simple, straight forward and very specific. No long winded explanations or examples, exactly what i needed while preparing.

👌🏾 [Kubernetes for Developers course (LFD259)](https://training.linuxfoundation.org/training/kubernetes-for-developers/) by The Linux Foundation.

Sadly i didn't finish this nice course as i ran out of preparation time and didn't want to postpone any further the date i had scheduled for the exam.

💪🏾 [Docker and Kubernetes: The Complete Guide](https://www.udemy.com/course/docker-and-kubernetes-the-complete-guide/) by [Stephen Grider](https://www.udemy.com/user/sgslo/) also via [Udemy](https://www.udemy.com/).

Whilst i didn't go through this course for certification purposes it's a very good course and add's to your general knowledge and skill using Kubernetes. Buckle up though as this one is ***VERY*** lengthy and if you follow along with the examples and hands-on then it would even take more time to complete. I went through this course as a precursor to my exam preparations and i absolutely enjoyed it, comes in highly recommend.


### CKAD Practice Tests

These tests really really helped me, i can't say it enough, i went though each twice. First pass through was abit rough in some areas, so i found out what i was doing wrong, filled the knowledge gaps and circled back.

* [CKAD Exercises](https://github.com/dgkanatsios/CKAD-exercises)
* [Practice Enough With These 150 Questions for the CKAD Exam](https://medium.com/bb-tutorials-and-thoughts/practice-enough-with-these-questions-for-the-ckad-exam-2f42d1228552)
* [CKAD Resources](https://github.com/lucassha/CKAD-resources)
* [CKAD Crash Course](https://github.com/bmuschko/ckad-crash-course/tree/master/exercises)

*Thanks to all the authors who took their time to put these questions together.*

### Vim Guides

Vim and Nano can be used for the exam. If you intend to use Vim then you could get up-to-speed with these tutorial listed below and always use Vim when practicing so you get familiar with the commands and shortcuts. Resist the Atom, Sublime or VSCode urge 🤓.

* [Getting Started with Vim: An Interactive Guide](https://scotch.io/tutorials/getting-started-with-vim-an-interactive-guide)
* [Vim - Quick Guide](https://www.tutorialspoint.com/vim/vim_quick_guide.htm)
* [Vim Crash Course | How to edit files quickly in CKAD / CKA exam](https://www.youtube.com/watch?v=knyJt8d6C_8&ab_channel=TheFrontOpsGuy)

*Thanks to all creators of these content.*


### Other materials
Some other materials i found informative via Youtube and thought to mention here

* [CKAD Certified Kubernetes Application Developer Certification Exam Tips](https://www.youtube.com/watch?v=dIBX8TQJxW8&ab_channel=CloudandBeyond)
* [Office Hours | Hands-on Tips to Pass the CKAD Exam](https://www.youtube.com/watch?v=L6K_8dOFR5w&ab_channel=CloudAcademy)
* [Tips on preparing for Certified Kubernetes Application Developer (CKAD)](https://www.youtube.com/watch?v=rnemKrveZks&feature=youtu.be)

*Thanks to all creators of these content.*
