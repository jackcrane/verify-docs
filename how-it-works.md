---
layout: default
title: How JVerify works
nav_order: 3
permalink: /how-it-works
---

# Learn how JVerify works and how you interact with it.
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## The Flow

Verifying a phone number is a 2-step process:

Send a `POST` request to the `start` endpoint with your API key and the user's phone number. This will create an irreversable hash of some relevant information and will be returned to you. You will need this hash for the `verify` endpoint.

For the `verify` endpoint, submit the hash and the pin number from the user.

For security purposes (and because there is no real reason to do so), the PIN is only sent to your user's phone. It is never sent to your application or our databases.