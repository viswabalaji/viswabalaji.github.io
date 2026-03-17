+++
title = "Reflections on Amazon Bedrock Inference Team"
date = 2024-09-02
draft = false
tags = ["ai", "infrastructure", "career", "distributed-systems", "engineering-culture", "amazon-bedrock", "aws"]
description = "Two years of lessons learned building and scaling Amazon Bedrock's AI model inference services, from model fine-tuning to Anthropic integration."
+++

In September 2023, I moved to a new team that was being setup. I didn't know who my manager was going to be or the exact nature of this project. Later I'd find out that we'd be integrating Anthropic's Claude models into AWS infrastructure as part of the new [Amazon-Anthropic partnership](https://press.aboutamazon.com/2023/9/amazon-and-anthropic-announce-strategic-collaboration-to-advance-generative-ai). Two years later, I've helped scale one of the fastest-growing AI services in AWS history.

[Amazon Bedrock](https://aws.amazon.com/bedrock/) processes millions of inference requests daily for Fortune 500 companies and startups alike. High availability, low latency, incredible rate of feature delivery (pre coding assistant era) required careful thought and management in a competitive market. In my first year, I helped launch the [model fine-tuning feature for Anthropic's Claude models](https://aws.amazon.com/blogs/machine-learning/fine-tune-anthropics-claude-3-haiku-in-amazon-bedrock-to-boost-model-accuracy-and-quality/) from private beta through GA. In my second year, I worked on Anthropic model inference services, maturing them into production-grade systems. I've since moved to AWS's Agentic AI organization. My core motivation for writing this document stems from the quote *"You don't learn from experiences, you learn by reflecting on your experiences"*—a foresight my first manager in the industry instilled in me. My core reason for sharing these reflections broadly was inspired by the quote *"We make our living by what we get, but we make our life by what we give"*.

## The Challenge: Controlled Chaos

Bedrock culture can be described in two words: **"Controlled Chaos"**. The speed of execution is unlike anywhere else in AWS, driven by intense customer demand and a competitive market where OpenAI, Google, and Anthropic release new models every few months. This velocity opened incredible opportunities but meant making pragmatic trade-offs to re-think and innovate existing development and deployment practices without compromising security. Speed is a double-edged sword that opens great doors but requires constant vigilance to maintain quality standards. Having a strict bar was crucial to maintain these standards.

I transferred internally after a rigorous interview loop (2 coding, 1 system design). This signaled that the hiring bar remained in this fast-moving organization. After joining, I started participating in internal and external hiring loops, helping hand-pick team members. This took my external interview count from 0 to 30. Regular interviewing helps beyond just showcasing for "Hire and Develop the Best". Interviewing lets me connect with job seekers, learn candidate assessment and calibration, and practice conflict resolution techniques from Amazon Bar Raisers in hire/no-hire calls.

Once the right set of people are in place, the other most valuable resource to Bedrock was the prized hard-to-get inference servers themselves. Accelerated compute (GPU/Trainium) capacity is treated like gold. It is not uncommon to spend days negotiating with the capacity planning team for additional servers. Every team has innovative ideas and high priority customers who are waiting to get unblocked. To help leaders see the signal between all the noise it was important to advocate for your use case, potential impact, opportunity loss. These often resulted in intricate trade-off analyses and leadership escalations. This scrutiny ultimately controls the levers for sales and go-to-market teams regarding how and which customers to prioritize across regions. The increased scrutiny also brings valuable access to Senior Principals, Directors, and VPs who want to understand utilization patterns and use cases in depth to ensure Bedrock trends in the necessary direction.

## Technical Journey: From Fine-Tuning to Production Inference

One of my goals joining AWS, was to help lead a launch of greenfield service from 0→1→100. Bedrock's model fine-tuning feature gave me that opportunity to take a feature from proof of concept to private beta for internal customers, then public preview and ultimately GA (generally available). The constant question was *"Can this be done faster? What do you need for that?"* . The reward for good work is more work, so need to carefully take on and distribute load across the team. You have to learn to disagree and pushback often, otherwise burnout becomes a real risk without proper boundaries. You also learn when to commit to ambitious decisions as well to keep the team humming at a constant pace. This backpressure naturally arises out of Amazon's leadership principles but requires the individual contributors to be able to advocate for their work or disagree with leadership boldly.

The project went through the full cycle: PRFAQ (press release / frequently asked questions written before building), private beta, public preview, and general availability. We navigated application security reviews, operational readiness reviews, GDPR audits, and multiple vendor security assessments.

Though model fine-tuning is now less appealing compared to run-time reasoning inference, the key learnings of building workflows and distributed systems dealing with GPUs fundamentally transformed how the team operates. Due to capacity constraints, compute had to used across projects this required us to explore concepts such as time-sharing, auto-releasing capacity, maximization utilization with job batching whenever possible. Most importantly, we automated capacity movements via APIs instead of manual action or code deployments via dev ops pipelines in EKS clusters—a key differentiator that helped scale the inference team.

The model finetuning team eventually merged into the Anthropic inference service team, which became one of the fastest growing teams in Bedrock. Working on Anthropic model inference provided valuable operational experience. Pattern recognition develops quickly—you learn to assess customer impact within minutes and drive resolution systematically. In addition you'd start building systems and mechanisms to auto-resolve these issues or re-design to prevent them in the first place.

Working daily with tenured engineers (Principals and Seniors), I absorbed their approach to disambiguating problems, writing docs for different audiences, and driving cross-team initiatives. They empowered others to act independently rather than constraining communication through themselves. This agency was crucial for skill development.

We worked closely with data science teams to evaluate model quality, assess fine-tuning effectiveness, and run trust and safety evaluations. The data science team became our first customer, providing early feedback on user experience. This tight feedback loop helped us stay customer obsessed from the start.

## Culture and Operations: How the Team Actually Worked

### Technical Excellence Under Pressure

My biggest learning was to **give the service itself (not just customers) the rightful dignity.** Maintaining service quality requires constant attention to detail, cultural commitment, and deliberate effort. The system's design will be questioned, scrutinized, and debated. It's easy to blame the "service" for not meeting expectations, but remember that the service is a function of decisions we made or choose to make.

Respecting the service ensures we all move the needle in the right direction daily. You need to trust that incremental improvement will compound over time. When you fix low-hanging fruit and minor inconveniences, you'll naturally become more motivated to invest additional time in the system. Others will see your effort, and the flywheel continues. Important to not let broken window theory settle in.

You should push back and maintain high standards because that's the duty of technical team members. Let managers push back on project timelines, then disagree and commit as needed. When making pragmatic trade-offs under deadline pressure, document the ideal outcome so the team can iterate toward it later. The pace of innovation and rate of requirements change may be extreme, but using mechanisms to regulate decisions and trusting the process is essential. Tinker with the mechanisms themselves instead of just reacting to outcomes.

### Communication Patterns That Emerged

Developers rely heavily on Slack, and you could be part of hundreds of active Slack channels including long-running threads on model expansion channels, temporary discussion channels, customer escalation channels, and team channels that change as functions of re-orgs. Chat gets overwhelming quickly, but features like reminders and saved items help immensely. Without these tools, leaving messages as unread was the only way to ensure you'd return to messages requiring deeper thought.

SOPs were heavily misused in our environment. We had SOPs for non-standard operations and abnormal system behavior that actually needed fundamental fixes rather than permanent workarounds enshrined as documentation. The SOP mechanism exploded to the point where on-call engineers struggled to find them, didn't know they existed, or found them frequently outdated.

My preferred design philosophy is entirely different: let the system auto-correct errors whenever possible. We eventually automated 80% of our incident responses through self-healing systems, but getting leadership to approve the upfront investment time was the real battle. Most issues can be self-healing if implemented well. It was important to provide developers space to build resilient, self-healing systems. The challenge was once the happy path starts working, then justifying additional effort to harden the system before release becomes a tough conversation especially when intensive competitive pressures exist. This challenge exists across the industry due to rate of expected progress. A thought experiment is whether all companies could benefit from building more stable solutions if teams had one proper break day a week.

With lot of short term solutions, systems don't self-heal and on-call engineers need a reasonable mental model of the system or guardrails present to prevent bad operations from happening. Critically, alarms need to be setup correctly to identify when things aren't as expected. Knowing when to reasonably compromise vs when to start strongly advocating for resiliency is also an important skillset for a senior engineer to possess.

### Design Reviews and Project Management

Weekly design review meetings are traditional opportunities for stakeholders to learn, debate, and align. In fast-moving environments, however, weekly cadence isn't sustainable because priorities change within hours. Requirements flow in and out, developers lack time to scope edge cases in advance, feedback providers face time constraints, yet projects still have strict deadlines. The only path forward becomes *"design on the fly."*

This approach caused significant churn as mental models kept changing and different people ended up with different views of the system. Follow-up sessions were needed for anti-entropy, but it was hard to find time when everyone was neck-deep in operations or other projects. The trade-off between streamlining and speed of execution consistently favored speed, though investing upfront in design alignment yielded better outcomes whenever we managed to do it.

Design reviews should have differing levels of preparedness depending on the context. Developers must advocate for proper preparation time because saving 30 minutes of prep & deep research time can sacrifice 60 minutes of 10 developers' time with an unstreamlined discussion. My personal favorite design philosophy: *"The goal is achieved not when there is nothing more to add, but when there is nothing left to take away."*

For these design on fly projects, "war room"-style project approach usually produced the best results. Putting relevant people together for high-quality, quick conversations, implementing and iterating without distractions proved tremendously beneficial. This was a shift in development pattern as industry moves from general peace-time feature development to war-time market acquiring competition phase win the AI domain. One area where the organization evolved was having **ONE** and only **ONE** sticky manager for a project. A manager with sufficient technical depth pays strong dividends because manager <-> dev interaction on complexity and timeline is short and sweet. From an org-wide standpoint, regular status report emails by project leads or managers helped improve visibility into projects, common blockers, and learnings.

### Operational Incidents

The best communication mechanism in fast-moving orgs is often a well-defined incident tracking system. At our org, we had severity levels similar to SEV2/SEV3 classifications, with specific escalation paths and correction-of-error (COE) and root cause analysis (RCA) processes following major incidents.

Forged by fire, I picked up skills to assess customer impact, drive resolution, come up with short-term and long-term mitigations, and construct feedback for customers, account managers, and internal clients. This operational expertise is hard to get in steady-state teams with occasional issues. Use operational events to showcase your ability to lead. Proactively propose mitigation strategies, assess customer impact, suggest recommendations and fixes, escalate and delegate as necessary rather than overburdening yourself.

In fast-moving environments, it's tempting to normalize operational challenges when "need for speed" becomes the justification. However, maintaining high standards for service quality is essential. We need to put faith in and instill dignity into the service we maintain. Service reliability is a commitment to customers, so we need to give it appropriate importance. This mindset is vital to maintain in fast moving teams.

Try to own fixes and think big about actions we could have taken to avoid being more than X steps away from disaster—not just the last step before disaster. Research what other large teams did to deal with these issues. The amount of solved problems and accumulated knowledge in large organizations is high; learning to discover and reuse them is a skill itself.

## Technical Architecture: What Running AI at Scale Actually Looks Like

The service uses distributed systems architecture with separation of concerns between management and execution layers. Standard cloud-native patterns provide modularity across API, security, and operational components.

Wherever there were good microservice-style contracts between teams, life was smooth; otherwise, integration complexity increased. Some of the initial integrations were hand-managed and had to be revisited for API style integration with right level of coupling. Postponing this leads to knowledge silos (or worse lost context) and ownership gray areas in addition to operational pain.

The best way to understand the system is to map the journey of a single inference request from customer to the inference service and back. Learn about different types of requests and what features they activate: streaming output, extended prompt caching, thinking and reasoning modes, tool use, images, content moderation for input inconsistent with acceptable use policy. Spending time to understand key log lines and fields with their meaning and caveats, gives outsized return, saves hours of debugging and reveals how complicated distributed system tracing can be if not well planned.

For control plane work, we embraced TypeScript with full strict type system for CDK (Cloud Development Kit) infrastructure as code— best learning was to NOT work around types. We designed DynamoDB tables with several composite indexes with full idempotency, state transition protection, and metadata support for audit trails. DynamoDB's recent support for [multi-attribute composite keys in global secondary indexes](https://aws.amazon.com/about-aws/whats-new/2025/11/amazon-dynamodb-multi-attribute-composite-keys-global-secondary-indexes/) makes this pattern even more powerful. Know the ins and outs of Step Functions workflows, both periodic and event-triggered. Learn how topology plays an important role when placing workloads and understand "boundary units" intuitively: availability zones, regions, cells.

For infrastructure scaling, learn what peripheral components need to co-exist in addition to GPU, Trainium, and Inferentia nodes for inference to function smoothly: monitoring agents, log collectors, network plugins, storage drivers. You don't need to be a Kubernetes expert (it's a daunting deep task)—learn abstractions and deep dive as necessary. Understand cellular architecture and how to expand in multiple dimensions: region, cell, model. Scaling large EKS clusters across several regions and cells exposes all possible failure conditions. Amazon EKS recently announced support for [ultra-scale AI/ML workloads with up to 100k nodes per cluster](https://aws.amazon.com/blogs/containers/amazon-eks-enables-ultra-scale-ai-ml-workloads-with-support-for-100k-nodes-per-cluster/), which reflects the scale challenges teams like ours face daily. AWS has also been experimenting with [disaggregated inference architectures](https://aws.amazon.com/blogs/machine-learning/introducing-disaggregated-inference-on-aws-powered-by-llm-d/)—our architecture has some similarities but isn't exactly the same, as we optimize for different trade-offs based on our specific workload patterns.

## What Changed in Me: Career Growth

The key lesson that Bedrock imparted was to not just blindly work, but **CREATE A NARRATIVE** of the work you do and try to integrate all new work into an existing narrative or start a new narrative, but it must all form a good story. If you try doing this then automatically you will get all the following benefits:

- You will learn to consider impact of work
- You will learn to prioritize tasks better to prefer work that leads to stronger narratives
- You will improve your overall perception in the team as a subject matter expert (even though you weren't directly involved in every design and decision made). You will also put in extra effort to learn extensively and reason about system edge cases to keep the perception of an SME
- If you prioritize the right work at the right time and become an SME, your manager will give you better projects
- On project success, your manager will have a strong narrative for themselves about delegating tasks effectively
- Customers will love the narrative and be attracted to great products delivered in record time

> **P.S.** Great narratives of your work also make interviewing much easier, as the experience provides excellent examples of leadership principles in action.

End of the day, the goal is to have great narratives, so for this you need to do the right work at the right time.

Two years at Bedrock feels like five years of career growth experience compressed. I went from proudly having zero production incidents in my previous team to diligently running incident response for a service handling millions of inference requests daily. I learned that growth primarily relies on taking more ownership of projects and features, essentially developing the force of nature inside you to become the person who pushes things ahead. You don't just react but start crafting and pushing your own agenda.

Working on customer operations on-call rotation and tech escalation rotation gave me perspective on customer use cases and expectations, both reasonable and extreme. I saw the scale at which AI usage is growing internally (other teams within the company) and externally (startups and enterprises). Enterprise customers have vastly different requirements around compliance, data residency, private endpoints, and custom SLAs compared to startups.

I learned that it's vital teams spend as much time on model trust and safety setup and maintenance as on the inference model itself. This is a critical, non-negotiable part of the system. Strict evals, tighter data sharing boundaries, and least privilege implementation look like friction initially but are critical safeguards.

Scaling and maintaining large Kubernetes clusters across several regions and cells exposes all possible failure conditions. Investing in the right abstractions and frameworks early helps maintain code clarity and prevents error handling from obscuring core logic. Monitoring and self-healing systems at different levels are crucial. Embrace both event-driven and periodic anti-entropy systems—they can co-exist.

Prefer to reuse. Spend more time investigating existing solutions before rebuilding unless there are strong reasons not to. Large organizations have significant accumulated knowledge—learn to discover and reuse it. You CAN and MUST reach out to principal engineers and senior engineers in other teams, and they would love to collaborate. Don't limit yourself to just public documentation for use case fit.

Be observant, empathetic, compassionate. This is easier said than done in fast-moving orgs with tight deadlines. Simple rule: try to simplify the life of future people maintaining your code, doc, design, interface, system. If you take on complex tasks, elucidate and simplify the mental model when done. Take time to inquire what others are doing and **ASK** them why they're doing that. Several interesting conversations and ideas begin this way. This is one of the easiest ways to force multiply—find synergies, eliminate redundant work, simplify complex designs just by being curious and empathetic.

## Looking Forward: Where AI Infrastructure Is Heading

Bedrock's Anthropic inference service is well positioned to continue growing. Newer Claude models with more advanced capabilities, trust and safety requirements, and billing complexity will keep arriving, and the team is ready to pass on all improvements, optimizations, and cost savings to customers with AWS-level support.

AWS is also investing in multiple inference architectures beyond just Anthropic. The team behind [Mantle](https://aws.amazon.com/about-aws/whats-new/2025/12/amazon-bedrock-responses-api-from-openai/), the cutting-edge inference engine currently powering OpenAI APIs through Amazon Bedrock Responses API, represents exciting innovation in this space. This shows AWS's commitment to supporting diverse model architectures and giving customers choice in how they build AI applications.

The industry is evolving rapidly toward specialized inference hardware. AWS's recent [partnership with Cerebras](https://www.aboutamazon.com/news/aws/aws-cerebras-ai-inference) for ultra-fast inference and the continued investment in Trainium chips demonstrate where future models are heading—purpose-built hardware optimized for specific inference patterns. As models become more complex with extended reasoning capabilities, the infrastructure supporting them will need to evolve in parallel.

I've already seen several developers 10x their output by effectively utilizing agentic coding companions. The powerful positive flywheel is here: we're using agentic AI to iterate and improve AI infrastructure to the next level. AI is going to be more tightly integrated with daily human life and activities. Any time you feel overwhelmed, take a step back and consider if AI can help. The fundamental unit of "reasoning" can be applied broadly to many problems.

Even if AWS doesn't have the best foundation model in-house yet, enabling the application layer, enterprise integration, and quality of service can be key differentiators. So if you're considering joining Bedrock to work on integration at the layer closest to the model, go for it!

## Acknowledgments

Words fall short to describe the impact the people of Bedrock had on me. Sincere thanks to everyone I've worked and interacted with. It was an absolute pleasure and wonderful journey. Tribute to all the Bedrock members for all the hard work they put into scaling and running a world-class system in this shapeshifting AI market.

I have already seen several developers who have 10x'd their output by effectively utilizing agentic coding companions. The powerful positive flywheel is here: we are using agentic AI to iterate and improve AI infrastructure to the next level. AI is going to be more tightly integrated with daily human life and activities. Any time you feel you are overwhelmed doing any style of work, take a step back and consider if AI can help you with it. The fundamental unit of "reasoning" can be applied broadly to many problems.

Following the trend of ["Reflections on X"](https://calv.info/openai-reflections) posts, I hope this reflection helps others considering similar work. The exposure supercharged my ability to work on insanely fast-moving teams, learning to ruthlessly prioritize and work on the art of perception and communication skills. Learning state-of-the-art security practices, inference model hosting architectures, and AI model applications gives a great window into predicting how the future will look.

## References

- [Reflections on OpenAI](https://calv.info/openai-reflections) - Calvin's blog
- [Amazon and Anthropic Announce Strategic Collaboration to Advance Generative AI](https://press.aboutamazon.com/2023/9/amazon-and-anthropic-announce-strategic-collaboration-to-advance-generative-ai) - Amazon Press Release
- [Fine-tune Anthropic's Claude 3 Haiku in Amazon Bedrock](https://aws.amazon.com/blogs/machine-learning/fine-tune-anthropics-claude-3-haiku-in-amazon-bedrock-to-boost-model-accuracy-and-quality/) - AWS Machine Learning Blog
- [Amazon EKS Enables Ultra-Scale AI/ML Workloads with Support for 100k Nodes per Cluster](https://aws.amazon.com/blogs/containers/amazon-eks-enables-ultra-scale-ai-ml-workloads-with-support-for-100k-nodes-per-cluster/) - AWS Containers Blog
- [Amazon Bedrock Responses API from OpenAI](https://aws.amazon.com/about-aws/whats-new/2025/12/amazon-bedrock-responses-api-from-openai/) - AWS What's New
- [Amazon DynamoDB Multi-Attribute Composite Keys in Global Secondary Indexes](https://aws.amazon.com/about-aws/whats-new/2025/11/amazon-dynamodb-multi-attribute-composite-keys-global-secondary-indexes/) - AWS What's New
- [Introducing Disaggregated Inference on AWS Powered by LLM-D](https://aws.amazon.com/blogs/machine-learning/introducing-disaggregated-inference-on-aws-powered-by-llm-d/) - AWS Machine Learning Blog
- [AWS and Cerebras Partner on Ultra-Fast AI Inference](https://www.aboutamazon.com/news/aws/aws-cerebras-ai-inference) - Amazon News

---

*Views expressed in this blog are my own and do not represent the views of my current, past, or future employers.*
