# **basode: balanced software development lifecycle**

# **attitude framework \+ implementation guide v0.55**
License: GNU GPLv3. Author: akos@docca.org

What is it balancing? I discuss the issues of balancing our own professional lifetime-attention and token cost, and I write down minute-lived thoughts and solutions, and I try to collect new ones that live new minute-lives. Because we are in a permanent revolution, like socialism according to Lenin.

Related endeavors, tags:

* spec driven development (SDD)  
* harness engineering, platform engineering  
* delivery system-target system  
* expert attention as a bottleneck

**The main challenges: dealing with PRD and finding the optimal AI-human division of labor**

*This part is pretty dry, it's worth skipping. It might be trivial in retrospect.*

Very succinctly, this doc is about how, in the case of a software development project \- considering the situation in Q2 2026 \- the main task in my opinion is:

- very conscious and effective management of the Product Requirements Document (PRD), (“name of the game: manage the PRD at the lowest cognitive effort”)  
- and the resources that implement PRD changesreasonableThis second one is talked about a lot in software circles \- on YT, X, etc., in the form of various SDLCs, harnesses, skills, workflows, rules, etc. \- so I will talk less about it here: because it is highly product-dependent, changes quickly, and because other sources are better than what I can say about it.

Note: the development organization discussed here is the same as the development carried out by human programmers, however, the weights have shifted due to the different strengths and weaknesses of agents and human programmers (e.g. price, speed, perception of the big picture).

The above sayings are consequences of this simple situation:

- Agents \_really\_ do what we ask them to do. What we ask them to do is PRD.  
  - Therefore, it must be clear that strict adherence to the SSOT and DRY principles in relation to PRD is one of the keys to the project's efficiency.  
  - Should the PRD be complete? This is not necessary, and in fact, it is usually not possible (since we usually start with an idea \- which will develop as we test the first version). It is also advantageous if the initial PRD is as close to complete as possible, because in the first development iteration, i.e. during the first “mega-sprint”, we get a pretty good result relatively cheaply.  
  - We need an effectively manageable PRD. This is what I propose here \- and I assume that many new ideas will emerge as this recognition becomes widespread.  
    - In short, the point is: we prefer to tell stories about how the system works, and then expect a dry, logical, traditional spect from that AI (which we don't even have to read). Why? Because it's easier for us to write about what happened, to understand that the*“Dezső received a new job in an email, he created a project for it in the system with the “new job number” function, and invited Ági, Feri, and Kati, who thus saw all the docs that had already been saved for this project, and they can even participate in editing them, and they should, since they are the ones who translate the docs into three languages.”*\- as tech spect writes:*“left menu: “new job number”, this creates a new JOB NUMBER object to which USERS can be assigned. USER has RW rights to the FILE objects belonging to JOB NUMBER.”*  
- Let's protect our wallets:The agents' abilities are limited, but they are happy to try as long as our wallets or other resources allow. Although the agents' attempts are also costly, managing partial results is also costly \- due to the value of human time. The task is therefore to find the optimum in this.  
- (??? let's make a short version of this and find room for the longer one ???) We work with at least two users: agents are like a remote-controlled robot in our apartment: it can do useful things, but it can also do seemingly random scary things, or you can completely take control of it from a distance, and its behavior can change. Therefore, we only let it into a part of our apartment where there is no chainsaw, no bank card, no gasoline can and no matches, \- in fact: there is no phone with which it can freely take action, e.g. order gasoline and matches.

  Returning to the analogy with the AI ​​agent: several things can divert its behavior in a harmful direction different from our intention, so the agent's activity must be isolated from the possibility of causing damage. Therefore, we must start the development in an isolated environment (e.g. a separate VM), and within that with multiple OS and git users: we have our own, elevated user (e.g. the developer “tom”) \- and one or more users for the agents (in practice, e.g. a user named “toms\_agent\_for\_dev”), and we must be in the Linux shell with this second one when we start claude. Why is this necessary? Because the agent's behavior can unexpectedly deviate from what we consider good, and can cause brutal damage in a matter of seconds: it can publish or delete things we did not intend to publish or delete (git repo, secrets, etc.). The reason for this may be:  
  - bad intent: an offensive, malicious prompt injection,  
  - accidental: anything we cause during our interactions, or any text that accidentally enters the agent's prompt or context.

## **1\. Why is this necessary? The code is ready in minutes and it's a snap...**

The human's job in a software development project these days is this: they tell the AI ​​agent what to do, and then they check the product, giving feedback to the agent \- until it reaches the desired state.

I had a good little Antigravity project. For some reason I don't like this term "vibe coding" \- maybe because I like programming \- but you could call it that. It started out really well, I was hacking and spinning for weeks, staying awake until dawn, only forward, making my dreams come true. But... But the small discrepancies kept growing, sometimes this, sometimes that, sometimes the agent went into the bushes so much that I could barely repair the damage. For some reason that was incomprehensible to me, it started to chew on things that it had prepared cleanly days before \- in worse cases, it started to break up the previously well-functioning parts. It burned the tokens and my enthusiasm. Slowly, I found myself with a multitude of smaller and larger tasks hanging over my neck, and I also started to feel that whatever I touch, everything is connected to everything, I get deeper and deeper into holes, and I no longer remember the previous sunny world where I was soaring recently. I found myself in an oppressive spider's web, with a thousand little threads pulling here and there, and although each one seemed manageable, as I pulled one, many more came with it.

At that time, I had to do some self-examination and also look at my project. What am I actually doing? I am constantly refining the operation of the AI ​​agent \- rules, skills, etc. \-, I am shooting at a moving target with my product \- since I am currently developing it \- and I am constantly adjusting my infrastructure and deployment processes accordingly. Chaos? Followable? What do I need to do now?\!

This framework of thought \- balanced SDLC (basode) \- is for those who are experiencing more or less similar things, or if they are smarter than me: they recognize in advance that they want to avoid this.

### **Balance in the division of labor between humans and AI agents**

Since in agentic development, programming and programmed testing work is already carried out autonomously, without humans, the length of the project is determined by the number and parallelism of the remaining tasks of classic development projects \- left to humans \- and the cost is determined by the labor and tokens.

The fundamental goal of basode is to optimize the human-AI division of labor. In other words: the main organizing aspect of a software development project: optimizing delegation to AI. If we optimize for efficiency \- or in my case: I want to avoid burnout \- then one of the main rules is something like this: it is a bad decision to take over any work or responsibility from the AI ​​model in areas that AI can do more economically.

How can we tell where this limit is? It varies. The economies are also determined by how much we can control the agent's behavior, and how much we can detect its outputs that differ from the desired result with little human effort and at an early stage \- after burning a little time and tokens. If this is a corporate project, then the poorly chosen limit or faulty control starts to eat up money \- and if it is a hobby project like mine: the developer gets tired, drowns in his tasks. Why? Because partly because it involves work that requires brutally high cognitive focus and time: precise and clear specifications, and thorough reading and correction of result docs, and on the other hand, brutally a lot of woodcutting work: testing. At least that's what happened to me. This was the trigger that gave rise to this conceptual framework \- because this pressure was my personal experience in AI-supported development. If this is vibe coding, then I'm tired of it.

The two lessons I learned: the role of the human actor is well minimized when:

* Defining a task for the agent: here, strictly minimal transformation tasks must be undertaken.*We will come back to this \- because it is of key importance. In short: I define the task with a tale, a story.*  
* Execution control: validation of the result also places the least amount of time and cognitive burden on the human actor.  
* Feedback: with minimal effort. One of the most labor-intensive parts of the entire cycle can be to provide accurate feedback.

That's what this paper will be about.

—-

### **The requirements specification (PRD): the program of development. Therefore, it requires a lot of attention.**

In a traditional software development environment, the consistency of project management is provided by the soft skills of human actors, formalized SOPs (Standard Operating Procedures), as well as unwritten community norms and implicit job descriptions.

With the advent of AI programming agents, this situation has changed. Agents have no local knowledge beyond the data they are shown; their operation is strictly limited to the context explicitly provided. The rules of engagement are no longer part of the culture of the development community (company, open source community, etc.) floating outside the project, but are part of the project’s inseparable, code-like (**Docs-as-Code**) components.

A **basswood**framework responds to this architectural constraint. It converts previously soft, human-driven processes into a machine-interpretable, traceable set of rules. This radically expands the boundaries of AI agents' autonomy, enabling them to independently execute more complex and vertically extended development iterations.

## **2\. Similes: the water surface of a lake and double-entry bookkeeping**

One of the main pillars of basode is the transparency of the current state of the system: it should be clear at a glance whether the system is stable or undergoing changes.

### **The lake and the waves**

A stable system is a lake with a mirror-smooth surface. A new demand is an impulse that someone threw into the lake. A basode is a collection of physical laws that model the operation of the lake with the goal of eventually making the water surface smooth again.

If the modeling is bad (the physics is bad), the interaction of demands does not subside, the lake is constantly in a state of upheaval, and a lot of resources (tokens, time, money) are spent on restoring the water level. Good physics ensures that every stimulus – be it a large rock or a small water lily – leads to a quick and effective calming.

### **The accounting analogy and double-entry bookkeeping**

In accounting, the effect of a new account (impulse) takes its place in the year-end balance sheet at the latest, creating the new balance. In the case of a transfer account, the record is divided into two:

1. The business result is immediately affected by the amount of the document (liability).  
2. On another level, we await the physical movement of funds (fulfillment) in the bank account.

Basode adapts this principle. It is a strict obligation to "book" every new request in two places:

* **Status Outage in Documentation (SSOT):**We enter it in the operational documentation of the finished system with an explicit WIP (work in progress) designation.  
* **Status push in task manager (Kanban):**A task is created that awaits physical realization.

As we perform the operations on the task, the "debt" disappears with the implementation: we close the ticket and remove the WIP mark from the documentation. The lake has calmed down, the stone has reached the bottom, a new stable state has been created.

## **3\. Simply: two systems, the target and the delivery system**

### **The dual product model (3D printer analogy)**

Basode's claim is that \- in a minimal situation \- we are developing two systems simultaneously:

* **Target System (Product A \- the "cup"):**The software product to be created.  
* **Delivery System (Product B \- the "3D Printer"):**The manufacturing method, infrastructure and process system. Modification of the Delivery System always takes priority over the Target System, as a poorly calibrated production line cannot produce a good product.

### **Two-layer… and N-layer product docs (doclayer1…N)**

A significant part of a system that requires human work \- in the age of agents \- is the requirements specification \- I call this the first layer of documentation, doclayer1 for short. From this, agents can diligently produce the final product, either in one step or in several steps, i.e. through several doclayers.

* **doclayer0 (Ideas):**Unstructured, non-systemic demands (the flying stone).  
* **doclayer1 (TargetSpec):**The greatest human responsibility: human-verified requirements specification, the global SSOT (the splash stone) \- in the case of software, this is the PRD.  
* **doclayer2...N-1 (Transitional models):**Technical specifications, diagrams where actors specify the task. BDD, Gherkin.  
* **doclayerN-a (Active implementation):**Tests, running source code and infrastructure (lake bed and water).  
* **doclayerN-b (Passive projections):**User manual, knowledge base, etc. The system automatically generates these at the end of the cycle (information boards on the lake shore).

### **Triage is Instant-issue**

* **Triage:**The moment when the idea gets the green light and double-entry accounting occurs (WIP injection and Kanban ticket opening).  
* **Instant-issue:**The equivalent of an "Andon cord". In the event of a critical bottom-up error, the pipeline immediately freezes, and an emergency impact assessment and repair follow.

## **4\. Let's complicate: many systems are systems, like onion skins \- and the tulip: the product**

In reality, the concepts of "Target" and "Delivery" are relative \- although in a significant part of the cases they are sufficient (Pareto principle, and let's not complicate things unnecessarily). Our system: consists of a recursive chain of systems. Our system: the bulb of a tulip. We start with the idea \- this is the seed of the tulip, from which the bulb begins to develop, as it develops, we create more and more shells, finally a wonderfully complex result, from which, in a lucky case, a beautiful flower will grow.Firstthe outer shell: the project initiator creates the project goals, the following roles shape it into a product, set up development workflows (agent-friendly SDLC, harness), infrastructure, etc., and the innermost core, the product we create.

* **syslayer1: idea, vision, FOUNDER**  
* **syslayer2a: Delivery Architect, DA (or any other title. The point is: the person responsible for the SDLC rules, harness building and maintenance. E.g. implements the basode for the situation.)**  
* **syslayer2b: Product Owner, PO.** Creates a requirements specification, i.e. target system/doclayer1, from the vision (initial docs) and user requirements (stones thrown into the pond, or ideas coming to the kanban, bug fixes, etc.)  
* **(I don't use this: syslayer2c: Marketing**It takes the vision to the “market”: to the community, to investors, to customers, etc.)  
* **syslayer3a: responsible for infrastructure and deployment, INFRA (or DevOps, or Platform Engineer):**responsible for dev infra, and CI/CD plans and pipelines. Could be a syslayer2c as well, I have 3a now  
* **syslayer3b: DEV, (a Dev Tools / Agents):**Development rules, agent profiles, scripts.  
* **syslayer4 (Product):**The inner core (business specification and code). This is the beautiful tulip that the onion created: from the outer shell \- the idea/vision, the onion developed, and from its innermost part, the tulip emerged.

Task tracking in this model can be distributed (in the case of separate teams) or monolithic (in agentic development), where metadata on a single Kanban board indicates the affected onion skin.

## **5\. \-**

## **6\. The relationship of our framework: ancestors, collateral relationships**

Basode was not created in a vacuum, but rather a collection of proven industry standards and architectural principles to optimize AI agentic development. The framework draws inspiration from the following systems and adapts them for the Agentic AI era:

### **Content and methodological overlaps**

* **Accounting (IFRS / Double-entry accounting):**Deterministic, synchronized recording of transactions for system stability. WIP injection and Kanban ticket management are analogous to managing business results and bank account balances.  
* **Agile / Kanban:**Iterative execution and visual task tracking (pull system), which clearly indicates the tension in the system.  
* **ITIL / ITSM (Change Management):** A basode *idea-to-delivery*The Change Management cycle is a modern adaptation of the classic ITIL Change Management process. The Request for Change (RFC) lifecycle in ITIL – from capture, through triage and approval, to implementation and closure – is logically identical to the Basode state-driven Kanban and WIP injection method. In addition, the Instant-Issue process is closely related to the ITIL Emergency Change protocol and the Andon cord interruption mechanism.  
* **GitOps / DevOps:**Treating infrastructure, processes, and specifications as code (Docs-as-Code), ensuring the primacy of the Single Source of Truth.

### **The most important theoretical intersections**

* **Maintainability (DRY \+ SOLID \+ LoD):**The goal of all three principles is to ensure that if we tamper with the system in one place, it does not break in ten other places. Basode provides this architectural isolation by separating the Delivery and Target System layers, as well as the doclayer and syslayer.  
* **Simplicity and focus (YAGNI \+ SMART \+ Pareto principle):**Each of these principles protects focus. They say:*"Don't do more than is absolutely necessary to achieve your goal."*This is critical in Agentic AI development: it serves to minimize token and context window consumption by agents, as well as unnecessary "wave-beating".  
* **Correctness and consistency (SSOT \+ DRY):**This duo guarantees that there are no two different versions of the same truth (be it data, algorithm, or specification). Despite double-entry bookkeeping, basode strictly protects the single source of truth (SSOT), preventing state fragmentation.

## **7\. Exercise: things to consider**

### **7.1. Specification: Production of SSOT (Transformative role)**

Before starting the project, a strategic decision must be made: what will be the top-level description mode of the system (doclayer1 SSOT), and what will be the derived (read-only) outputs (doclayerN-b)?

Main options for building a normative SSOT:

* **I DO NOT RECOMMEND: Traditional requirements description / User Manual style:** (pl. *"The system must be able to generate reports in PDF format..."*). From a human perspective, it is extremely labor-intensive and cognitively exhausting to maintain.  
* **RECOMMENDED: Stories / Scenarios Collection:**Narrative, story-based specification (User Stories/Scenarios). Much more natural for the human actor, but advanced LLMs are excellent at generating precise technical requirements and architecture from this.

### **7.2. Control, understanding: human responsibility \- and work**

Human focus is an expensive resource. The two biggest time-consuming activities in the validation phase are:

1. **Human testing (UAT):**Manual, human testing of the implemented, running product.  
2. **Responsible audit of generated documentation:**Reviewing and approving the final (result) documentation. In practice, this means that the “user manual” type documents are just as much outputs of the agent's work as the program code \-\> I don't write the user manual documents or the program code, the agent writes them.

These tasks should be planned and, where possible, agent-based verification tests (e.g. E2E Gherkin) should replace human clicking.

## **8\. Practical application (Playbook)**

### **8.1. Greenfield project checklist**

For a startup project, the introduction of basode begins with a deterministic self-assessment:

1. **Identifying systems:**What are the syslayers (e.g. is there dedicated DevOps)?  
   * I create a “docs\_of\_\$role” or “docs\_of\_\$syslayer” folder for each one, and in it “dl1\_of\_\$role” and … “dlN\_of\_\$role” folders, sometimes dl1a\_of\_\$role and and dl1b\_of\_\$role \- but it's a matter of personal taste whether someone breaks it down into folders below or up here.  
2. **Defining documentation levels:**How long does doclayer1 last, and what are the read-only outputs?  
3. **Kanban topology:**Is it optimal to have your own task tracking for monolithic (one board) or individual onion skins?  
4. **Simplification (YAGNI):**The goal is to strip down the structure to the bare minimum to avoid unnecessary complexity (minimal folder structure, basic .agent/\* files).  
5. **WIP wrapping syntax:**  
   * Everyone can make their own, this is an example. Note: for those who use Obsidian or other md editors, I see in retrospect that there are standard solutions \- they are worth considering. At the same time, they are somehow limited, so in the end there was not such a strong argument for me to migrate to them.  
   * So now I would do it a little differently, but for now:**\*\*WIP \[CREATE\|BUGFIX\|DEPRECATED\] kanban=”any\_NNN”\*\* …here is the content… \*\*/WIP kanban=”any\_NNN”\*\***  
   * **Bugfix vs change request:**It is natural that if we accept an idea (change request), we indicate this on DL1 by injecting WIP. A bugfix is ​​different: on the one hand, here we indicate that it is a bugfix with an internally agreed syntax. On the other hand, it is not included in DL1 as a final modification, but temporarily. The purpose of its existence in DL1 documentation \- with WIP injection \- is that if several reports are received regarding the same bug, we can filter out and reject new, identical requests before they “splash into the pond”, since we see by the bugfix-WIP entry that this topic is already on the agenda. As soon as the sdlc flow ends for this ticket, i.e. as soon as the error has been fixed, the entire WIP injection can be removed from DL1.  
   * “Deprecated”: during distillation, the part enclosed by wrappers is deleted.  
6. The “implementation\_plan” provided by Antigravity is a useful document, but needs to be converted:  
   * Option 1: for kanban tickets \- and if you are creating a completely new topic, then the appropriate SSOT documents must also be created by the agent (and this is where a significant portion of the WIP injections from the new kanban tickets come from)  
   * Option 2: in some cases it can be programmed directly, if we can guarantee that it will be documented in the doclayers afterwards, if the tests are unnecessary, etc. \- i.e. for some reason the SDLC steps can be omitted, and we don't want to burn tokens in order to comply with formalities. Obviously: be careful with this, because the savings may backfire.

### **8.2. Migration of a running project (Brownfield / Rescue)**

When an existing agentic project shows "signs of disintegration" (context loss, token burning, faulty code changes), the basode implementation follows the following strict iteration:

1. **Defining an ideal state:**Outline the target structure based on point 8.1.  
2. **Gap analysis (Self-assessment):**What is the reality? (Folders, processes, .agent file status, SSOT fragmentation).  
3. **Migration plan:**Let's create agent-driven scripts, tests and create the missing documentation layers. This is an AI task in itself \- but reviewing, improving: here's the work\! It's hard, time-consuming, uncreative \- and therefore dangerously boring task.**Attention\! This is the essential task\!**For me, for example, this was a 230-page generated doc, tales, stories, this is where the conceptual confusions and repeated stories that contained the same thing with slight differences, but previously misguided the development, emerged.  
4. **Conversion of open kanban tickets:**Creating WIP injections. This is done by an agent, there are two cases:  
   * The ticket is about a situation already described in dl1. In this case, in the docs, we only wrap the part that is to be implemented by the ticket in WIP \- and we may write the injection back to the ticket in the WIP field, but this is not so important.  
   * A ticket is an idea, a “stone in the pond” of change that has not yet been introduced to DL1. In this case, it is a full WIP injection: both to the ticket and to DL1.

*Comment:*In the Basode framework \- at least in my case \- the syslayers (the onion skins) are under continuous development until the very end of the project. The differences between human-machine expectations also continuously affect the elements of the system.

### **8.3 basode reorganization: separating thickened layers**

This occurs when the emphasis changes compared to the initial state, and for example a testing component changes from a small agent workflow step or a rule to a separate documentation layer. I don't have a developed idea for this, more like practice. I'll introduce this in the previous chapter: the task is the same in structure, but smaller in scope.

## **9\. Setting up and maintaining agentic operations: harness development (Claude Code and AGY)**

To ensure that administration and double-entry bookkeeping are not a human burden, the autonomous agents themselves must be trained to comply with the rules of the basode.

### **Agent Skills**

* **Information allocation logic:**The agent must be able to decide which syslayer and which doclayer a newly generated or received piece of information belongs to based on the DRY and SSOT principles. It cannot duplicate, but must append to the existing location or create a pointer.

### **Agent Rules**

* **SSOT Registry iteration:**When searching for information, the agent must be familiar with the documentation chain. It must know how to navigate from the top narrative level to the technical implementation without getting lost in the context.  
* **Injection permission (Write protocol):**Controlled access and syntactic discipline (placement of \<wip\> markers) when entering information into documentation systems, guaranteeing the synchronicity of double-entry bookkeeping.

### **Agent processes (AGY: workflows, CC: commands)**

Here are the steps of SDLC (in my case: specification, test development/development, testing, distillation), as well as maintenance processes.

## **10\. Dilemma due to DRY+SSOT: the tightness of helper scripts and their help**

ADevelopers (and POs and people in all sorts of positions) use a lot of utility programs (let's call them scripts), e.g. for system startup, debugging, regular conversions, etc. (I'm talking about the utility programs typically in the scripts/ or hack/ folder.) To work efficiently, you need to know these and always use the right ones \- otherwise you'll waste time and tokens.

One practical maxim is: I create an agent skill for each one ([https\://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) \~ [https\://antigravity.google/docs/skills](https://antigravity.google/docs/skills) ).

Since we strive to ensure that the code (helper scripts in particular) and its documentation are very closely linked (usually available as "script \-help"), the goal is that if we are writing something in the script, the documentation itself should be very much in front of the actor's eyes (in context).  
Opportunities I found \- but also a dilemma:

- shdoc, POD and similar code \- as comments \- integrated doc creation. Disadvantage: DRY violation. Because:  
- a [SKILL.md](http://SKILL.md)In \- due to the operation of the agentic development systems \- the same information is mandatory. To resolve this: the scripts are[skill.md](http://skill.md)They show their own help from a specific frontmatter field. Disadvantage: their help and docs are not tightly tied to the script.  
- [skill.md](http://skill.md)I have some reservations about this at the moment, because it takes it away from its place in the project where it can be found with a human brain, and \- by putting it in the skill folders defined by the harness \- makes it harness specific.

## Appendix 1: todo, pre-merge ideas

### isolation agent due to hijacking threats \#harness\_engineering

When establishing a project: separate operating system users for agents, also distinguish them by name, so that we humans never forget not to share anything that could cause trouble with that technical user object.

### (git) repo isolation onion skin layers

vagy minimum: delivery system, target system

### Delivery system file versioning

important because without knowing which state caused the problems, it is difficult \- rather: impossible \- to implement effective self-corrective measures: scrip, skill, command, hack

### Agent\<-\>human dialogue format: instead of chat, a file edited/commented by agent+user

Basically, we should work on plan\~canvas\~artifact files, and chat should be the exception.

Why? Because the chat cannot be followed with the human eye/brain: if we get a list of 15 errors at one point in the chat, and we go into one of them and start dealing with it, how do we review how we compare the other 15, especially if they have a cost impact. I think it's better to use it like a docx or google doc: the parties agree on it by commenting and tracking changes \- no basic information is lost here, and we can edit the doc until we have brought all the issues to a standstill. So, I think the important aspects for this are:

#### dialogue that is easy to follow with human eyes

as with a docx/g docx: it should be clear what was said, easily noticeable, clearly, what is the fresh, open branch, and what are the questions and answers from previous rounds, what topics are answered,

#### keep-it-lean: continuous relegation of irrelevant parts (decided things, junk)

from the vital file to a cleaner doc. why?

* without this it will be difficult to see what else needs to be discussed  
  * and the existence of the file in the context, moving it back and forth, consumes a lot of tokens  
  * The doctor will need the clean one in the end.

#### The plan file we are talking about should be in a good place.

CC: the location of the plan file ("plansDirectory") can be defined in the config (settings.json). Considerations: how common is it with other human workers on the project (i.e. should it be in git), and how project specific is it, and how it fits into the workflow under "practical solutions", i.e. optimize it so that we don't have to copy the file edited by the harness/agent and the one we also view-annotate-modify.

#### practical solutions

I'm sure there's a better solution to this and I'd like to learn from someone. So far I've looked into these directions:

##### AGY: artifact

this was basically very okay when I used AGY until spring 2026

##### CC terminal: ctrl+G

For this, it is worth choosing a handy terminal editor. The advantage is that it is very token-friendly.

##### CC desktop app: plan-re dialogue skills development

I only found a token-expensive solution for this, so it's convenient but expensive \- at the same time, it's one of my main uses because it's convenient, fast

##### harness neutral solutions:

###### *via ping-pong (git) repo, including a friendly md editor, e.g. Obsidian*

agent's living space dev-\>git-\>user friendly desktop md editor (e.g. Obsidian) we look here, edit \-\>git-\>dev (I'm pretty lame when it comes to git, so I have a constant struggle with this, but for those who are more skilled, I think it's two clicks \- this is also one of my main uses)

###### *Via VS Code*

agent's environment dev-\>direct access (e.g. ssh tunnel) VS Code, we view it here, edit it \-\> save or push it back to the agent environment.  
I couldn't find a solution to edit the file in md viewer (md source ctrl+shift+V), only in the source, which is okay, but I don't gain anything from it compared to the terminal, so I discarded this \- but it could be that I just haven't found the right VS Code plugin

### The dangers of a mad, malicious agent: the “remote-controlled robot in our home” analogy

The story of “my robot went crazy, sold my house for 1 croissant, cut open our family safe with a chainsaw, then called a locksmith to open the front door, let himself in, and left.” To shed light on the boundaries of sharing tools and secrets, and how to balance our efficiency and security.

### Efficiency: agent-managed test environments

The harness/platform engineer's job: it is very important that the agent can work independently: modify, write and execute tests, debug. Without this, the soaring will stop: human logging will hang our well-oiled SDLC. Determine the importance of human contribution in uat and prod environments, especially: secret management and cost constraints.

### Cost control

Harness/platform engineer's job is to find cost containment options that limit the outgoing token or other resource costs. Setting SaaS provider limits, alerts where hard limits are not possible.

### Resource control

When an agent starts working, it doesn't automatically pay attention to whether the disk, memory, or anything else is running out or is being used unreasonably. Therefore, it is the harness/platform engineer's job to insert these aspects at the appropriate points. It happened to me: my VM suddenly became inaccessible, so I had to do a hard reboot because the agent started so many docker containers that killed the machine \- and because of that I lost valuable development states, since this session kicked the machine out of the hands of other sessions.  
