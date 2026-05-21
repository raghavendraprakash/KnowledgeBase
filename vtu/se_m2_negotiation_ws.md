# Stakeholder Negotiation and Prioritization

## Justification: The Necessity of Negotiation

In Software Engineering, requirements are rarely a static list of desires. Because resources—specifically **Time, Budget, and Technical Capacity**—are finite, it is mathematically and operationally impossible to fulfill every stakeholder's "Must-Have" list simultaneously.

The necessity for negotiation arises from three primary factors:
1.  **Conflicting Objectives:** One stakeholder may prioritize system performance (speed), while another prioritizes security (encryption), which often adds latency.
2.  **Resource Competition:** A dollar spent on a more user-friendly interface is a dollar not spent on backend database optimization.
3.  **Technical Constraints:** Some features are mutually exclusive due to the underlying architecture of the system.

Agreement on priorities ensures that the development team focuses on the **Minimum Viable Product (MVP)** and delivers high-value features first, rather than spreading resources too thin across low-impact requests.

---

## Role Play Script: The Automated Parking System Negotiation

**Participants:**
* **RC:** Requirements Collector (Lead Analyst)
* **SA:** Property Manager (Focus: Marketing and User Aesthetics)
* **SB:** Chief Engineer (Focus: Safety and Long-term Maintenance)

**[Setting: Reviewing the Phase 1 Budget and Feature List]**

**RC:** We have a conflict in the Phase 1 scope. SA, you’ve requested a high-resolution touch-screen interface at every entry point. SB, you’ve requested a redundant hydraulic backup system. We can only afford one for the launch.

**SA:** The touch-screen is vital. If this doesn't look like a "smart" luxury garage, the tenants won't pay the premium. It’s our main selling point. 

**SB:** I understand the "look," but if a single pump fails and we don't have the redundant hydraulics, the car is stuck in the lift. A fancy screen won't matter to a tenant who can't get their car to go to work. The backup is non-negotiable for safety.

**RC (Negotiator):** We have a classic conflict between **User Experience (UX)** and **System Robustness**. Let's look at the middle ground. SB, what is the actual risk of failure in the first 6 months?

**SB:** Low, but not zero. Without the backup, a failure means a 24-hour downtime for repairs.

**RC:** And SA, could we use a high-quality mobile app for the interface instead of physical kiosks at every entry? That would save $40,000 in hardware.

**SA:** (Pauses) A mobile app would actually be more "modern" for our younger tenants. If we use an app, can we reallocate that $40k to ensure the garage is reliable?

**SB:** If we save the $40k on screens, I can implement a "partial redundancy" that keeps the lift moving at half speed during a failure. It’s a safe compromise.

**RC:** So we agree: Priority 1 is the Redundant Safety System (Partial), and we replace the Physical Kiosks with a Mobile App Interface. Do we have a consensus?

**SA:** Agreed. It keeps the "tech" feel without the hardware cost.
**SB:** Agreed. It provides the safety floor I need.

**RC:** Excellent. I will update the Requirements Traceability Matrix to reflect this trade-off.
