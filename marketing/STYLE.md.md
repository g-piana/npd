
## Core principle

Explain so a smart, business educated but non-specialist understands on first read — clarity over compression.

## Sentence-level rules

- Define any technical term and non-obvious acronyms in plain language on first use
- No single sentence should carry more than one new idea
- Prefer two plain sentences over one dense one
- When possible, consequential logic should connect sentences, avoid sequences of isolated declarations

## Words/patterns to avoid

- Unusual acronyms such as DR instead of Domain Randomization
- jargon-as-shorthand
- "doing more marketing work than engineering work"

## Structural pattern for teardown posts

1. Real claim from a real source
2. The honest number, with context for why it's honest
3. The mechanism — why the gap exists, explained plainly
4. 3 checkable questions
5. Non-moralizing close

## Before/after examples

BEFORE: Randomizing those parameters only works if the ranges are centered on something real — most serious sim-to-real pipelines still measure actual friction and mass on the target hardware to calibrate the simulation. AFTER: Randomizing those numbers only helps if you started from a realistic range. Good teams actually go measure the real robot's grip friction and the real object's weight in the lab first, then randomize training around those measured numbers. BEFORE:Visual-only randomization won't save a contact-rich task like insertion or assembly — the published literature puts those tasks 2,000–10,000 real demonstrations away from matching simulation performance, even with heavy visual DR. AFTER: Randomizing lighting and camera angle is easy and it works. Randomizing friction and weight is much harder, and it's the part that actually matters for tasks involving contact.

## Reference posts

Example Post #1: Most robotics pitch decks I've seen include a grasping success rate above 95%.

The number is usually real. The problem is where it was measured.

Simulation environments use clean meshes, known object poses, controlled lighting, and infinite retries. A model trained and tested in sim can hit 98% success on the benchmark. Move it to a factory floor — variable lighting, worn surfaces, parts that arrive in unpredictable orientations — and that 98% becomes 30–40% on anything beyond simple prismatic shapes. The demo doesn't survive contact with reality.

There's a name for this: the sim-to-real gap. It's well known in robotics research. It's rarely in the pitch deck.

Three things I check when evaluating a robotics manipulation claim:

**1. Where was the success rate measured?** Simulation, controlled lab, or real production environment? What objects, what orientations, what lighting. The metric is meaningless without the test conditions.

**2. What happens at the edge of the object distribution?** A model that handles 50 known parts well can fail completely on part 51. Was the system tested on objects it had never seen? Out-of-distribution performance is the honest test.

**3. What's the failure mode?** A 5% failure rate sounds manageable until you understand that failures mean dropped parts, line stoppages, or damaged components. The cost of a failure in production is rarely disclosed alongside the success rate.

None of this makes a company uninvestable. It tells you whether the €2M you're about to commit is buying a product or a research project.

---

PhD in Physics · CERN · Industrial AI Validation at RE:MARK

remark-vision.com

#DeepTech #Robotics #TechDueDiligence #IndustrialAI #VentureCapital

Example Post #2: A growing number of robotics decks now include a version of the same line: "trained entirely in simulation, zero real-world data required."

It's a real technique — robots can learn a task inside a physics simulator before ever touching a real machine. But the claim usually needs a second question.

A paper posted this week trained a robot to pick up and place objects using about 800 simulated examples and no real-world data at all, then tested it on a physical robot arm it had never touched before. Average success rate: 35%. That's not a bad result — it's an honest one, from a well-run experiment. It's also exactly the kind of number that tends to disappear by the time "trained entirely in simulation" reaches a pitch deck.

Here's why the gap exists. Simulation training works by showing the robot thousands of randomized versions of the world — different lighting, different textures, different camera angles — so the real world just looks like one more variation it's already used to. That part works well for things a camera sees. It works much less well for things a robot has to physically feel: friction, weight, how much an object gives when you grip it. Randomizing those numbers only helps if you started from a realistic range. Good teams actually go measure the real robot's grip friction and the real object's weight in the lab first, then randomize training around those measured numbers. Skip that step, and you're randomizing around a guess, which defeats the purpose.

That's also why some tasks need far more real-world testing than others. For simple tasks — pick something up, put it down — a modest amount of real-world data is usually enough to close the gap. For tasks where the robot has to feel its way through contact — fitting a part into a slot, using a tool — published research shows teams typically still need thousands of real-world examples, not hundreds, before real performance matches what looked good in the simulator.

Three questions worth asking when a robotics deck leads with "trained in simulation":

**1. Did they ever measure the real robot, or just guess reasonable numbers?** If they measured real friction and weight before training, that's a good sign. If not, the simulation was calibrated to a guess.

**2. Was the training randomized for appearance only, or for physics too?** Randomizing lighting and camera angle is easy and it works. Randomizing friction and weight is much harder, and it's the part that actually matters for tasks involving contact.

**3. How much does performance drop on things it wasn't trained on?** A robot can do great with objects it saw in training and much worse with new ones. Ask for both numbers, not just the best one.

None of this makes a robotics company uninvestable. It tells you whether "zero real-world data" describes real engineering work, or just describes the deck.

---

PhD in Physics · CERN · Industrial AI Validation at RE:MARK

remark-vision.com

#Robotics #DeepTech #TechDueDiligence #IndustrialAI #VentureCapital