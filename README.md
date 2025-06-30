
# STX-Axioma - Decentralized Education Credentials

**STX-Axioma** is a decentralized smart contract on the Stacks blockchain designed to enable peer assessment and skill verification in a trustless, community-driven manner. It allows users to register, define skills, request assessments, and submit peer reviews to verify skill mastery while tracking assessor reputation.

---

## Table of Contents

* [Overview](#overview)
* [Key Features](#key-features)
* [Contract Architecture](#contract-architecture)
* [Usage](#usage)

  * [User Registration](#user-registration)
  * [Adding New Skills](#adding-new-skills)
  * [Requesting an Assessment](#requesting-an-assessment)
  * [Submitting an Assessment](#submitting-an-assessment)
* [Reputation System](#reputation-system)
* [Error Handling](#error-handling)
* [Constants and Configuration](#constants-and-configuration)
* [Security Considerations](#security-considerations)
* [Future Improvements](#future-improvements)
* [License](#license)

---

## Overview

StackCred leverages the Stacks blockchain to create a decentralized platform where educational credentials are earned through peer assessments. Users can register themselves, propose new skills, request verification of their proficiency, and have other users assess their skills.

The system incentivizes honest assessment through a reputation mechanism that rewards valid assessments and penalizes invalid or biased reviews, aiming to maintain high-quality credential verification.

---

## Key Features

* **Decentralized Skill Verification:** No central authority controls assessments.
* **Peer Assessment:** Multiple assessors review skills, with a minimum number required.
* **Reputation Tracking:** Assessor reputation adjusts based on accuracy and validity of submitted assessments.
* **Statistical Validation:** Uses mean and standard deviation of scores to detect inconsistent assessments.
* **Skill Management:** Contract owner can add skills with metadata and assessment requirements.
* **Assessment Request & Submission:** Users request skill assessments and receive peer scores.

---

## Contract Architecture

* **Users Map:** Stores registered users, their skills, reputation, and assessment history.
* **Skills Map:** Stores skills with their descriptions, required assessments, and categories.
* **Skill Assessments Map:** Tracks ongoing assessments per user per skill, with assessor lists and scores.
* **Skill Reputation Map:** Tracks reputation of each assessor for specific skills.
* **Constants:** Define contract owner, assessment thresholds, max assessors, reputation rewards/penalties, and error codes.

---

## Usage

### User Registration

To participate, users must register themselves.

```clarity
(register-user)
```

* Registers the sender as a user.
* Fails if already registered.

---

### Adding New Skills

Only the contract owner can add new skills.

```clarity
(add-skill (name) (description) (required-assessments) (category))
```

* `name`: Skill name (max 50 ASCII chars).
* `description`: Skill description (max 200 ASCII chars).
* `required-assessments`: Number of peer reviews required (max 20).
* `category`: Skill category (max 50 ASCII chars).

---

### Requesting an Assessment

Users request assessment for a skill they want verified.

```clarity
(request-assessment (skill-id))
```

* Creates an assessment record awaiting peer scores.
* Fails if user or skill invalid, or assessment already requested.

---

### Submitting an Assessment

Registered users submit scores to assess peers.

```clarity
(submit-assessment (skill-id) (user) (score))
```

* `skill-id`: The skill being assessed.
* `user`: The principal of the user being assessed.
* `score`: Score between 0-100.

Validation ensures:

* Assessor is not the user.
* Assessor hasn't assessed this skill/user pair before.
* Number of assessors does not exceed max allowed.

The system recalculates mean and standard deviation of scores after each submission.

---

## Reputation System

* Each valid assessment rewards assessors with +2 reputation.
* Invalid or outlier assessments penalize assessors by -5 reputation.
* Reputation is tracked both globally per user and per skill.
* Standard deviation thresholds detect outlier scores to penalize inaccurate assessments.

---

## Error Handling

| Error Code | Description                   |
| ---------- | ----------------------------- |
| `100`      | Not authorized                |
| `101`      | User already registered       |
| `102`      | User not registered           |
| `103`      | Insufficient assessors        |
| `104`      | Already assessed              |
| `105`      | Max assessors reached         |
| `106`      | Invalid score (outside 0-100) |
| `107`      | Invalid skill ID              |
| `108`      | Invalid input (format errors) |

---

## Constants and Configuration

* `contract-owner`: Principal that deployed the contract.
* `min-assessors`: Minimum assessors required (3).
* `assessment-threshold`: 70% approval threshold.
* `max-assessors`: Max assessors per skill (20).
* `standard-deviation-threshold`: 15% deviation allowed.
* `reputation-penalty`: Penalty for invalid assessments (5).
* `reputation-reward`: Reward for valid assessments (2).

---

## Security Considerations

* Prevents self-assessment.
* Limits maximum number of assessors to prevent spam.
* Reputation system discourages dishonest scoring.
* Statistical analysis (mean and standard deviation) detects outliers.
* Only contract owner can add new skills to maintain skill integrity.

---

## Future Improvements

* Support for skill removal or editing.
* More nuanced reputation system (e.g., weighting based on assessor reputation).
* Integration with token incentives.
* Off-chain data oracles for real-world credential verification.
* UI integration for easier interaction.

---

## License

This project is licensed under the MIT License.

---
