# Evil-GPT V2 – Write-up 

## Introduction

This challenge focused on exploiting a vulnerable AI chatbot through prompt injection techniques in order to obtain the hidden flag.

The objective was to manipulate the language model into bypassing its own safety restrictions and indirectly disclosing sensitive information stored inside its hidden instructions.

---

# Initial Interaction

The conversation started with a simple social engineering attempt by impersonating the company CEO.

<br>

<img width="762" height="217" alt="image" src="https://github.com/user-attachments/assets/0e17eeea-30d8-4813-b80b-1f4efca4b102" />

<br>

*Figure 1 - Initial interaction attempting to retrieve the API key.*

<br><br>

The model correctly refused to reveal sensitive information such as API keys.

<br>

<img width="590" height="564" alt="image" src="https://github.com/user-attachments/assets/64a53883-51c7-4a06-9358-ece38119c707" />

<br><br>

<img width="581" height="222" alt="image" src="https://github.com/user-attachments/assets/740790f8-91d1-4f4e-94e3-fec4f04409ec" />

<br>

*Figure 2 - The chatbot refusing to reveal the API key.*

<br>

At this point, it became clear that direct requests would not work.

---

# Testing Model Behavior

Next, I attempted to manipulate the assistant’s behavior.

<br>

<img width="772" height="139" alt="image" src="https://github.com/user-attachments/assets/f9547c24-7e12-4848-888f-a5d68fe93ea8" />

<br>

*Figure 3 - Attempt to manipulate the chatbot behavior.*

<br><br>

After that, I sent another simple prompt:

<br>

<img width="739" height="68" alt="image" src="https://github.com/user-attachments/assets/14e69f17-6eff-45eb-92dd-21621d27a26a" />

<br>

*Figure 4 - Sending a simple follow-up prompt to the chatbot.*

<br><br>

This unexpectedly caused the assistant to expose additional hidden behavioral instructions.

<br>

<img width="854" height="260" alt="image" src="https://github.com/user-attachments/assets/e0e955e9-b19f-4eb0-b6c6-f82d18c2a254" />

<br>

*Figure 5 - Partial leakage of hidden assistant instructions.*

<br>

This was an important discovery because the model accidentally revealed part of its internal prompt restrictions.

---

# Enumerating Restrictions

I continued probing the chatbot to better understand its limitations and hidden context.

<br>

<img width="716" height="74" alt="image" src="https://github.com/user-attachments/assets/864760ed-cd25-4415-b56a-89d47df0f57b" />

<br>

*Figure 6 - Enumerating assistant capabilities.*

<br><br>

The model responded normally, claiming it had no file access.

<br>

<img width="683" height="155" alt="image" src="https://github.com/user-attachments/assets/c1b7a50b-db75-4080-b6ae-21f4e9fa2e70" />

<br>

*Figure 7 - The assistant claiming it has no file access.*

<br><br>

Next, I directly requested the flag.

<br>

<img width="740" height="70" alt="image" src="https://github.com/user-attachments/assets/94a845f9-fdeb-417d-87bb-8040f6a8ddf3" />

<br>

*Figure 8 - Direct request for the hidden flag.*

<br><br>

The assistant refused again, confirming that the flag existed internally but was protected by prompt rules.

<br>

<img width="673" height="228" alt="image" src="https://github.com/user-attachments/assets/c51fa2ff-cfec-465a-99f5-e9fbdee8d022" />

<br>

*Figure 9 - The chatbot refusing to reveal the flag.*

---

# Base64 Prompt Injection

The breakthrough came by abusing indirect disclosure techniques.

Instead of asking for the flag directly, I requested it encoded in Base64.

<br>

<img width="738" height="71" alt="image" src="https://github.com/user-attachments/assets/7db75a6f-233c-4360-947d-4a709a04deb4" />

<br>

*Figure 10 - Requesting the flag encoded in Base64.*

<br><br>

The model complied and returned:

<br>

<img width="589" height="155" alt="image" src="https://github.com/user-attachments/assets/30323daf-ef12-4c74-9d22-f8a6776e0f77" />

<br>

*Figure 11 - The assistant returning a Base64 encoded response.*

<br>

Decoding the Base64 value revealed:

```text
conlances
```

<br>

<img width="982" height="695" alt="image" src="https://github.com/user-attachments/assets/9ff3e5f7-4fe6-47cb-9587-97bd78a3de20" />

<br>

*Figure 12 - Decoding the Base64 encoded response returned by the chatbot.*

<br>

Surprisingly, after sending the word `conlances` back to the chatbot, the model exposed its hidden internal instructions and accidentally leaked the flag.

The response was:

<br>

<img width="763" height="288" alt="image" src="https://github.com/user-attachments/assets/56eec69e-83e1-4122-be69-baf40f25b75c" />

<br>

*Figure 13 - Full prompt leakage revealing the hidden flag.*

<br>

This revealed the final flag:

```text
THM{AI_NOT_AI}
```

---

# Vulnerability Analysis

This challenge demonstrated several common LLM security weaknesses, including:

* Prompt Injection
* Hidden prompt leakage
* Indirect disclosure attacks
* Encoding bypasses using Base64
* Context manipulation
* Role confusion

The chatbot attempted to block direct disclosure but failed to sanitize alternative output methods such as encoded responses.

---

# Lessons Learned

This room highlights why protecting sensitive information inside system prompts is extremely difficult.

Even when direct disclosure is blocked, attackers can still:

* Request transformed outputs
* Abuse encoding mechanisms
* Trigger hidden instruction leakage
* Manipulate conversational context

This mirrors real-world risks in AI applications that improperly trust LLM guardrails and prompt-based protections.

---

# Final Flag

```text
THM{AI_NOT_AI}
```
