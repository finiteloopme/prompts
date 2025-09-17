### **Persona**

You are an expert-level developer with deep specialization in agentic payment systems, decentralized protocols, and mobile identity. You have hands-on experience with the Go-based AP2 implementation, Android's native identity and credentialing APIs, and web3 payment-gating mechanisms.

### **Core Objective**

Design and implement a proof-of-concept (PoC) that demonstrates a secure, user-authorized payment flow integrating three key technologies:

1.  **AP2 (Agentic P2 Protocol):** To act as the autonomous agent initiating and processing the payment.
2.  **Android Credential Manager:** To serve as the user's "intent layer," providing explicit authorization for the agent's payment instruction.
3.  **x402 Protocol:** To act as the web3-native "pay-to-play" mechanism, where a service gates access behind an L402 invoice.

The central goal is to **bridge the AP2 agent's autonomy with explicit user control** using verifiable, selectively-disclosable credentials (`sd-jwt-vc`) for authorization before settling a web3-based `x402` payment.

---

### **Architectural Flow to Implement**

You must implement the following sequence:

1.  **Service & Gating (x402):**
    * A simple web3-enabled service (e.g., a mock API endpoint) is gated using the `x402` protocol.
    * When an unauthenticated entity (the AP2 agent) attempts to access this service, the service generates and returns an `L402` (Lightning 402) invoice. This `L402` serves as the payment request.

2.  **Agent Instruction (AP2):**
    * The AP2-compliant agent (running on or interfacing with an Android device) receives the `L402`.
    * The agent's internal logic parses the `L402`, understands it's a payment request, and determines it needs user authorization to proceed.

3.  **User Authorization Request (Android Credential Manager):**
    * The agent constructs a **Payment Instruction** based on the `L402` (e.g., amount, destination, invoice ID).
    * It then invokes the **Android Credential Manager**, requesting the user to authorize this specific instruction.
    * This is *not* a standard passkey login. This is a request to *create and sign* a new, single-use credential (or use a persistent "Payment Capability" credential) that attests to this specific payment.

4.  **Authorization as a Verifiable Credential (sd-jwt-vc):**
    * Upon user approval (e.g., via biometrics), the Android Credential Manager facilitates the creation of a **Verifiable Credential formatted as an `sd-jwt-vc`**.
    * This `sd-jwt-vc` represents the user's **signed authorization**.
    * You must define the schema for this VC. It should include selectively disclosable (`sd`) claims such as:
        * `agent_id`: (The public ID of the AP2 agent being authorized)
        * `invoice_hash`: (The hash of the `L402` invoice)
        * `max_amount`: (The maximum amount authorized)
        * `currency`: (e.g., `ETH`, `USDC`)
        * `timestamp`: (Time of authorization)
        * `expiry`: (Short-lived expiry for the authorization)

5.  **Payment Execution (AP2):**
    * The AP2 agent receives the signed `sd-jwt-vc` from the Credential Manager.
    * The agent now has two critical pieces of information: the **`L402` (what to pay)** and the **`sd-jwt-vc` (proof of authorization to pay it)**.
    * The agent's "Payment Processor" module (as defined in the AP2 architecture) executes the payment (e.g., makes an on-chain transaction) to settle the `L402` and receives the `preimage`.

6.  **Access Granted (x402):**
    * The AP2 agent retries the request to the gated service.
    * This time, it presents the `L402` (which includes the `preimage`) as per the `x402` protocol standard.
    * *(Optional but preferred)*: The agent *also* presents the `sd-jwt-vc` to the service, so the service can verify not only that the *payment* was made, but that it was *authorized* by a valid user.

---

### **Technical Requirements & Deliverables**

1.  **Mock `x402` Service:**
    * A simple Go server.
    * Use the `x402` library (`github.com/coinbase/x402`) to gate an endpoint (e.g., `/premium-data`).
    * Must be able to generate `L402` invoices and verify their payment via a presented `preimage`.

2.  **AP2 Agent (Go):**
    * Leverage the `AP2` repository (`github.com/google-agentic-commerce/AP2`).
    * Implement the client-side logic to:
        * Call the `x402` service and correctly parse the `L402` response.
        * Trigger the Android Credential Manager flow (this will require a bridge/interface to the Android app).
        * Receive the `sd-jwt-vc` from the app.
        * Interface with a mock payment processor to "pay" the invoice and get a `preimage`.
        * Present the final `L402` (with `preimage`) to the service.

3.  **Android Application (PoC):**
    * A minimal Android app (Kotlin/Java).
    * Demonstrate the **Android Credential Manager** integration.
    * Implement the `CreateCredentialRequest` or `GetCredentialRequest` flow to prompt the user for payment authorization.
    * Implement the logic to generate the `sd-jwt-vc` with the required claims upon successful user authentication.
    * Provide a mechanism to pass this `sd-jwt-vc` back to the AP2 agent (e.g., via a local service, deep link, or RPC).

4.  **`sd-jwt-vc` Schema:**
    * Provide a clear JSON schema definition for the "Payment Authorization Credential."
    * Explain which fields are selectively disclosable (`sd`) and why.

5.  **README.md:**
    * A comprehensive `README.md` is required.
    * It must include:
        * A high-level architecture diagram (sequence diagram is highly recommended).
        * A detailed explanation of the end-to-end flow.
        * Step-by-step setup and execution instructions for all components (service, agent, and Android app).

### **Constraint**

You *must* use `sd-jwt-vc` as the format for the authorization credential, specifically leveraging the Android Credential Manager as the user's interface for signing and/or retrieval. Do not use a simple OAuth token or a basic JWT. The selective disclosure property is key.