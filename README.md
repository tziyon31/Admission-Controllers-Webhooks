# 🧠 Kubernetes Admission Controller – Webhooks

A hands-on demonstration of a **Mutating Admission Webhook** that enforces and modifies Pod security settings in real-time.

This webhook:
- 🔒 **Denies** Pods that attempt to run as root without explicit permission.  
- 🧩 **Defaults** to `runAsNonRoot: true` and assigns `runAsUser: 1234` if no `securityContext` is defined.  
- ⚙️ **Allows** explicit root access only if `runAsNonRoot: false` is set.

---

## 📜 Step 1 – Creating a Secret for the Webhook Server

**We begin by creating a Secret that stores the TLS certificate and key for secure communication between the API server and the webhook.**

<br><br>

<p align="center">
  <img width="353" height="37" alt="cert" src="https://github.com/user-attachments/assets/9dd22fbc-401a-494e-a966-c1bba61a1612" />
</p>

<br><br>

<p align="center">
  <img width="573" height="48" alt="key" src="https://github.com/user-attachments/assets/b2563e75-990a-419f-ad2d-276fb635008d" />
</p>

<br><br>

---

## 🚀 Step 2 – Deploying the Webhook Server

**We deploy the Admission Webhook Server as a Pod, referencing the Secret we created.**

This webhook automatically injects or validates security context configurations based on defined logic.

<br><br>

<p align="center">
  <img width="484" height="573" alt="webhook server deployment" src="https://github.com/user-attachments/assets/04b5d03e-7bbe-47ec-b4f3-30460ba34fd5" />
</p>

<br><br>

<p align="center">
  <img width="361" height="85" alt="server running" src="https://github.com/user-attachments/assets/1b008759-0c20-4488-a594-c89a968fb8de" />
</p>

<br><br>

---

## 🧩 Step 3 – Creating the Webhook Service

**Next, we expose the webhook via a Kubernetes Service to allow the API server to communicate securely with it.**

<br><br>

<p align="center">
  <img width="220" height="191" alt="webhook service yaml" src="https://github.com/user-attachments/assets/fb2cfa98-d3dc-4a0d-999a-6e56c407902f" />
</p>

<br><br>

<p align="center">
  <img width="326" height="83" alt="service created" src="https://github.com/user-attachments/assets/ee842475-3916-4e65-94d7-d198faddf2ce" />
</p>

<br><br>

---

## ⚙️ Step 4 – Creating the MutatingWebhookConfiguration

**We define a `MutatingWebhookConfiguration` that registers our webhook with the Kubernetes API server.**

The webhook will trigger **only on Pod creation** requests.

<br><br>

<p align="center">
  <img width="423" height="474" alt="mutating webhook yaml" src="https://github.com/user-attachments/assets/533e0ef2-9ced-42dd-b2d9-fa2234b57092" />
</p>

<br><br>

<p align="center">
  <img width="573" height="91" alt="webhook created" src="https://github.com/user-attachments/assets/11237b33-49df-416a-9ed6-620ac779819a" />
</p>

<br><br>

---

## 🧪 Step 5 – Validating the Webhook Behavior

### ✅ Test 1 – Default Values Injection

**We create a Pod without specifying a `securityContext`.**  
The webhook automatically adds `runAsNonRoot: true` and sets `runAsUser: 1234`.

<br><br>

<p align="center">
  <img width="493" height="209" alt="default injection" src="https://github.com/user-attachments/assets/a951fa0c-6e03-47fd-bfb9-01dca353d82e" />
</p>

<br><br>

<p align="center">
  <img width="326" height="56" alt="default validation" src="https://github.com/user-attachments/assets/246bb5db-9d4d-4648-b153-acba320ceadc" />
</p>

<br><br>

<p align="center">
  <b>✅ It worked! Default values were successfully applied.</b>
</p>

<br><br>

---

### ✅ Test 2 – Explicit runAsNonRoot: false

**We test if the webhook respects explicit root permission (`runAsNonRoot: false`).**

<br><br>

<p align="center">
  <img width="498" height="293" alt="nonroot false test" src="https://github.com/user-attachments/assets/1544e0ce-875e-45c1-83a8-dfb3c2877948" />
</p>

<br><br>

<p align="center">
  <img width="304" height="48" alt="nonroot allowed" src="https://github.com/user-attachments/assets/0323b2c9-fbab-4ca7-ad68-9991f6f45f6b" />
</p>

<br><br>

<p align="center">
  <b>✅ It works! The webhook allowed explicitly configured root containers.</b>
</p>

<br><br>

---

### 🚫 Test 3 – Conflict Detection (Denied)

**We attempt to create a Pod with conflicting security settings.**  
The webhook correctly denies the request.

<br><br>

<p align="center">
  <img width="510" height="250" alt="conflict denied" src="https://github.com/user-attachments/assets/a8697166-e5a9-45f8-87e8-a15ded9980b5" />
</p>

<br><br>

<p align="center">
  <b>🧠 Magnificent! The webhook enforcement logic works perfectly.</b>
</p>

<br><br>

<p align="center">
  <img width="1078" height="106" alt="denied response" src="https://github.com/user-attachments/assets/eab5c938-f129-486f-b8b0-0a9d499abdc1" />
</p>

<br><br>

---

## 🧠 Key Takeaways

- **MutatingAdmissionWebhooks** dynamically modify or validate resources before creation.  
- Webhooks must use **TLS** for secure communication with the API server.  
- You can use them to **enforce custom security policies** like disallowing root containers.  
- Each webhook is defined via a `MutatingWebhookConfiguration` or `ValidatingWebhookConfiguration`.  

---

<p align="center"><b>Admission Webhooks give you dynamic, policy-driven control over every Kubernetes resource — before it even exists.</b></p>
