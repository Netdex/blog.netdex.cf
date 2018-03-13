---
layout: post
title: Creating a Digital Dead Man's Switch
keywords: dead man switch, dead man, security, death
description: Planning for post-mortem
---
# Contents
{:.no_toc}
* Table of Contents
{:toc}

# A Digital Will
Here's a rather morbid topic. Let's say you want some sort of "digital will" - things you want completed digitally after you are dead. Why not appoint a human executor? Because humans are fallible, and you don't know whether or not your testament will be carried out as originally planned.


# Dead Man's Switch (DMS)
A proposed solution, which is not new, is the concept of a dead man's switch implemented in software. Before I talk about digital dead man's switches, it would be helpful to know what a dead man's switch is.

Let's say that you are a train conductor, speeding down a rail at 30 mph. From a far distance, you see a bus stuck on the tracks - just far enough so that if you engaged the brakes, they would be spared. Suddenly, you have a heart attack and die. The train doesn't know you're dead - it will keep barreling along and plough right through them (there are definitely more fail-safes present with modern train systems, just bear with me).

The problem with this system is that it is not "fail-safe" - that is, when the unexpected occurs, it does not naturally tend to a safe state. How would you solve problem? Take a bit to think about it.

One solution is a physical dead man's switch - something that would be able to tell when the conductor is unresponsive, and stop the train immediately (i.e. apply the brakes) in response. An example implementation is a foot pedal that requires significant pressure to keep pressed. If the conductor were to lose consciousness, they would probably not be able to keep the pedal down, which would trigger the fail-safe and bring the system to a safe state (i.e. not moving).

# Digital Dead Man's Switch (DDMS)
The same idea applies with a digital dead man's switch, where we want a system to return to a safe state (or deadly state, depending on the situation) once a certain set of conditions are met (usually operator incapacitation or death). For example, the following situations may warrant the use of a digital dead man's switch:

- Wiping master keys to encrypted data, making data irrecoverable
- Releasing decryption keys to encrypted data already publically known
- Delivering information to certain individuals such as account passwords, banking information, etc.
- Archiving/deleting social media accounts

The specific type of digital dead man's switch I will talk about for the rest of this article are those that react to inability of the operator to make judgement and act on decisions. This criteria covers death and incapacitation as well. We will call this requirement the "trigger criteria".

In response to the trigger criteria, the DMS will carry out a predefined action, which we will call the "trigger action".

In order to talk about a digital dead man's switch, we can separate a digital dead man's switch into two components, **detection** (regarding trigger criteria) and **reaction** (regarding trigger action). Additionally, I will also talk about the concept of a **trusted host**, including distributivity.

## Detection
Before we carry out the trigger action, we must know if the trigger criteria has been met. Of course, there is no way for software to directly know if your trigger criteria has been satisfied (especially if it is more complex, like the one I mentioned earlier). Thus, we introduce the idea of a "criteria hint". The presence of a criteria hint is a sign (but not a guarantee) that the trigger criteria has been met. 

For the specific trigger criteria I mentioned earlier, there are several criteria hints which may be used. Any combination of the following hints can be used, with the goal being to have the hints accurately represent the trigger criteria. Criteria hints should be used together in a way (e.g. a weighted sum) that most accurately represents the trigger criteria.

Also note that some criteria hints may depend on others. For example, you might only allow Confirmation by Trusted Entity after Failure to Execute Periodic Action. You want low confidence hints to depend on high confidence hints.

### Failure to Execute Periodic Action (FtEPA)
The operator must periodically execute a predefined action that only the operator may perform. Failure to execute the action within a given timeframe constitutes presence of this hint.

An example: the operator must respond to a challenge issued by a trusted server (hosting the DDMS) periodically. The challenge may only be completed with knowledge that the operator is in the sole possession of.

It is important for the predefined action to require the judgement/decision making abilities of the operator, as that is what we are trying to verify. Thus, it must depend on information only the operator knows.

Since this hint depends on the operator, it may be given a high level of confidence. Other hints may depend on this one, since it has a high level of confidence.

### Confirmation by Medical Technology (CbMT)
The operator's well being is continuously verified via medical technology (e.g. vital signs monitor). Failure to confirm well being via medical technology constitutes presence of this hint.

An example: the operator has a heart rate monitoring watch, which will report to a trusted server (hosting the DDMS) when the heart rate is no longer present. 

Note that depending on the technology used, this hint may be easy to circumvent. Thus, it should be given a low level of confidence.

### Confirmation by Trusted Individual/Organization/Entity (CbTE)
The operator appoints trusted entity(s) that will use their own criteria hints to detect the trigger criteria. A threshold on the number of trusted entities required to constitute presence of the criteria hint may be used, depending on whether the system should be fail-easy or fail-hard.

A bad example: the operator gives their spouse a button which when pressed, will report to a trusted server (hosting the DDMS).

A better example: the operator gives their spouse a phone application that when explicitly utilized, will display a challenge issued by a trusted server (hosting the DDMS). The challenge response will be reported to the trusted server.

It is important for the spouse to be the only entity that can solve the challenge.

The second example is more secure, as it requires the identity of the trusted entity to be verified before they are allowed to exercise their permission as a criteria hint. Trusting an entity without verification is equivalent to trusting EVERYONE.

The level of confidence given to this hint should be related to how much the operator trusts the trusted entity. You may want to only activate this hint if a hint with a higher level of confidence is present (e.g. FtEPA).

### Pre-criteria Explicit Self Confirmation (PcESC)
The operator possesses a method to forcefully satisfy the trigger criteria (on a timer) as an anticipation that the trigger criteria will be met in the near future.

An example: the operator possesses a phone application that when explicitly utilized, will display a challenge issued by a trusted server (hosting the DDMS). The challenge response will be reported to the trusted server. The operator is about to undergo a surgical operation where they only have a 10% chance of survival. They trigger the PcESC criteria hint before the operation on a timer, such that if they do survive, they may cancel the hint.

It is important for the operator to be the only entity that can solve the challenge.

This hint should have a high level of confidence, since it depends on the operator.

You may find that the example may be similar to the one used for confirmation by trusted entity (CbTE). This is because they are practically the same, except for two things.

#### Anticipation
The criteria hints used by trusted entities in CbTE should only be present when your trigger criteria is satisfied.

However, the PcESC hint may be present **before** the trigger criteria is satisfied. It is the operator that anticipates (using their judgement) that the trigger criteria may be met.

Note that anticipation may also be used in CbTE as well, but the next point issues why that may be a bad idea.

#### Trust
The only entity that the operator ultimately trusts is the operator themselves. It may be the case that the operator is the only entity that may be trusted with the ability to anticipate meeting the trigger criteria.

Ultimately, it depends on the specific implementation.

## Reaction
Now that we have defined the trigger criteria, we want to react accordingly with the trigger action. There is not that much theory regarding reaction, except for a couple key points:

### Reaction Intensity
You may only want certain trigger actions to be activated depending on how confident you are in your criteria hints. For example, you might not want your hard drive wiped if just a friend said you were dead, because the action is *irreversible* (I'll talk about that more later). However, some safer actions can be taken even if you aren't confident in your criteria hints, such as locking your computer or phone, or wiping your browser history.

### Reversibility
Actions may be be either reversible, irreversible, or somewhere in-between (writing that sentence down didn't really add anything).

You may want to only carry out irreversible actions (i.e. wiping a disk) if you are extremely confident in your criteria hints.

Reversible actions may be carried out without less thought, as there is a lower consequence when they are incorrect.

### Testing
Trigger actions may fail. It is important to test them periodically to make sure they still work. It is also important to be able to detect whether or not they have failed, and have a backup trigger action in case.

### Examples
The following are examples of some trigger actions:

- Wipe encryption keys
- Wipe hard disk
- Send message to individuals
- Archive/delete social media

## Trusted Host
You may have noticed that I talked a lot about a trusted server while talking about criteria hints. This is where the majority of the trust in a DDMS is placed.

### Trusted Server vs. Trusted Individual
Why is a trusted server safer than a trusted individual?

People change over time. They are not reliable. You may trust them now, but you cannot say that you will 5 years from now. However, they may know better than you, and be able to carry out your task with their judgement.

A computer will carry out your task (without interference) without question, at the downside that they lack judgement regarding how to carry out your task in the best way.

### Guaranteeing Trust
It is important that the server you are using for your DDMS is completely under your control. No AWS instances, Google Compute Engine instances. It is important that your server constantly has power. It should also have tamper detection (you can even add this as a criteria hint).

### Distributivity
If your DDMS server fails, the entire system fails. It is important to have multiple DDMS servers in several locations, that can detect whether a fellow server has failed (you can even add this as a criteria hint).

## Additional Concerns
### Reversal
It should not be possible to reverse the "triggered" state. Once the DDMS has been triggered, there should be no way to "cancel" it, if you are confident in your criteria hints. Of course that may not be possible in reality, so adding a cancel operation that requires the operator may be important.

# Conclusion
I will update this article with any new information I gain periodically. Please let me know if I have made any mistakes, if anything is unclear, or if you have ideas you want to add.