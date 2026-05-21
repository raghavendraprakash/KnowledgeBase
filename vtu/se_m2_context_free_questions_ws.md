# Automated Parking System: Requirements Elicitation Workshop

## Part 1: Context-Free Questions
These questions are designed to be high-level and unbiased, focusing on the "who," "what," and "why" without suggesting specific technical solutions.

1.  **Who is the ultimate patron for this system, and whose needs must we prioritize?**
2.  **What problem or inefficiency is this automated parking system fundamentally trying to solve?**
3.  **What does a "successful" implementation look like to you in twelve months?**
4.  **Are there any existing environmental or operational constraints that this system must respect?**
5.  **What are the consequences of *not* building this system or maintaining the status quo?**

---

## Part 2: Role Play Script
**Topic:** Initial Stakeholder Discovery Session
**Participants:**
* **Requirements Collector (RC):** Lead Analyst.
* **Stakeholder A (SA):** Property Manager (Focus: Revenue and Tenant Satisfaction).
* **Stakeholder B (SB):** Operations Director (Focus: Maintenance and Throughput).

**[Setting: A conference room at the development site.]**

**RC:** Thank you both for joining. Our goal today isn't to talk about specific sensors or software modules yet, but to understand the "why" behind this project. I'll start with a broad question. From your perspective, SA, what is the primary objective of automating this garage?

**SA:** Honestly, it’s about density and speed. We are losing tenants because the current ramp-style garage is cramped, and it takes them 10 minutes to find a spot. I want a premium experience where they drop the car and walk away.

**RC:** Interesting. So, "Time to Exit" is a key success metric. SB, how does that translate to the operational side?

**SB:** Well, speed is great, but my concern is reliability. If an automated lift goes down during the morning rush, we have a total bottleneck. We need a system that can handle 50 cars an hour without a single point of failure.

**RC:** Understood. Reliability is the bedrock. SA, who are the secondary users? We've talked about tenants, but what about visitors or delivery services?

**SA:** We haven't fully decided. If we open it to visitors, we need a seamless payment gateway. But my priority remains the permanent tenants.

**RC:** Right. If the system were to fail, even for an hour, what is the most critical impact?

**SB:** Beyond just angry tenants, it’s a safety risk. If the automated system stalls while a vehicle is mid-transit, we need manual overrides that don't require an engineering degree to operate.

**RC:** That gives us a clear requirement for "Manual Fallback Procedures." To wrap up this initial dive, what is the one thing this system *must* do, or else you'd consider the project a failure?

**SA:** It must be faster than a human driving to the fourth floor.

**SB:** It must be maintainable by our current staff with minimal specialized training.

**RC:** Excellent. This gives us a solid foundation for our first draft of the functional requirements.
