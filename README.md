# N-ATLAS Technical Integration Document
### National AI Innovation Challenge Submission
**Project Application:** Gridstore  
**Core Innovation:** Native Speech-to-Commerce Intelligence via **N-ATLAS** (*Nigerian Atlas for Languages & AI at Scale*)  
**Submission Category:** Multilingual AI, Financial Inclusion & Digital Commerce

---

## 1. Executive Summary

In **Gridstore**, **N-ATLAS** (*Nigerian Atlas for Languages & AI at Scale*) is implemented as the central multilingual voice engine, eliminating language and literacy barriers in digital retail across Nigeria.

Over 70% of Nigeria’s retail transactions occur within informal, open-air markets—driven by micro-merchants, market women, and consumers who communicate predominantly in **Nigerian Pidgin**, **Yorùbá**, **Hausa**, and **Igbo**. Conventional digital commerce platforms rely exclusively on standard English text inputs, excluding millions of active economic participants.

By integrating N-ATLAS, Gridstore enables users to **speak naturally** in indigenous Nigerian languages and local market vernacular. The system ingests raw voice audio, conducts real-time dialectal transcription, maps colloquial terms and indigenous units into catalog entities, executes contextual product discovery, and generates culturally authentic spoken feedback in the speaker's native tongue.

---

## 2. The Problem Addressed by N-ATLAS

| Challenge in Nigerian Retail | Conventional Western AI / E-Commerce | Gridstore with N-ATLAS Integration |
| :--- | :--- | :--- |
| **Language Exclusion** | Fails on Nigerian Pidgin, Yorùbá, Hausa, and Igbo. Demands grammatical English. | Native understanding of 5 national dialects with phonetic and tonal resilience. |
| **Literacy Barrier** | Requires typed text queries and reading complex interfaces. | 100% voice-first: users speak their queries and listen to synthesized audio feedback. |
| **Marketplace Vernacular** | Cannot map informal foodstuff terms (e.g., *Garri Ijebu*, *Epo pupa*, *Titus fish*). | Deeply grounded in Nigerian foodstuff taxonomy, brand nicknames, and local commodities. |
| **Informal Units of Measure** | Fails on local trade units (*derica*, *mudu*, *paint rubber*, *kongó*). | Recognizes and converts traditional market measurements into merchant inventory quantities. |
| **Network Constraints** | High-bandwidth audio streaming fails on unstable 2G/3G networks. | Compact audio compression, base64 payload transport, and client-side fallback heuristics. |

---

## 3. End-to-End System Architecture

N-ATLAS operates through a tightly coupled multimodal audio pipeline spanning client capture, low-latency transport, multilingual reasoning, catalog matching, and localized speech synthesis:

```
┌────────────────────────────────────────────────────────────────────────┐
│                        USER SPOKEN INPUT                               │
│  "Abeg find two mudu of garri ijebu and one bottle of epo pupa"        │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│           CLIENT AUDIO INGESTION & FFT WAVEFORM ANALYSIS              │
│  - MediaRecorder API (audio/webm, audio/mp4, audio/ogg negotiation)    │
│  - Web Audio API AnalyserNode (64 FFT real-time decibel feedback)      │
│  - Tone Synthesizer (440Hz → 880Hz start chime for tactile reassurance)│
│  Source: components/NATLASVoiceAssistant.tsx                           │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │ Base64 Audio + Language Code + Catalog Context
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│                   N-ATLAS BACKEND REASONING ENGINE                     │
│  Endpoint: POST /api/natlas/voiceSearch (server.ts)                    │
│  Model: Multimodal N-ATLAS Multilingual Engine                         │
│  1. Ingestion: Raw audio byte processing & acoustic decoding          │
│  2. Dialect Grounding: Strict contextual prompting for Nigerian retail │
│  3. Token Extraction: Translates vernacular units into database query  │
│  4. Intent Classification: SEARCH_PRODUCT | FILTER_CATEGORY | ORDER    │
│  5. Response Synthesis: Warm, dialect-specific voice confirmation      │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │ Structured JSON Output Adhering to Strict Schema
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│                    GRIDSTORE CLIENT EXECUTION                          │
│  1. State Mutation: Real-time filtering of active merchant catalog     │
│  2. Visual Confirmation: Highlighting matching items & quantities      │
│  3. Spoken Feedback: speakNATLASFeedback() via localized speech engine │
│     "I don find two mudu of garri ijebu and epo pupa sharp sharp!"     │
└────────────────────────────────────────────────────────────────────────┘
```

---

## 4. Multi-Dialect Linguistic Grounding & Operational Matrix

N-ATLAS supports five primary linguistic profiles across Nigeria's geopolitical zones, trained and grounded on real retail workflows:

| Dialect / Language | Code | Spoken Voice Input Example | Extracted Intent & Normalized Query | N-ATLAS Spoken Audio Confirmation |
| :--- | :---: | :--- | :--- | :--- |
| **Nigerian Pidgin (Naija)** | `pcm` | *"Abeg check if you get 2 paint rubber of garri and vegetable oil"* | `SEARCH_PRODUCT`<br>`Keywords: "garri, vegetable oil"`<br>`Qty: 2 paint rubber` | *"I don check the store! We get garri and vegetable oil sharp sharp for you."* |
| **Yorùbá** | `yo` | *"Mo fẹ́ ra ìrẹsì àti epo pupa títí di ilé"* | `SEARCH_PRODUCT`<br>`Keywords: "iresi, epo pupa"`<br>`Intent: HOME_DELIVERY` | *"Mo ti wa ìrẹsì àti epo pupa fun yin o! Ẹ wo àwọn tí ó wà nílẹ̀."* |
| **Hausa** | `ha` | *"Ina son shinkafa mudu biyu da man ja yanzu"* | `SEARCH_PRODUCT`<br>`Keywords: "shinkafa, man ja"`<br>`Qty: 2 mudu` | *"Ga shinkafa da man ja da kake nema a cikin kanti!"* |
| **Igbo** | `ig` | *"Achọrọ m azụ Titus na osikapa derica abụọ"* | `SEARCH_PRODUCT`<br>`Keywords: "azu titus, osikapa"`<br>`Qty: 2 derica` | *"Achọpụtala m azụ Titus na osikapa maka gị ugbua!"* |
| **Nigerian English** | `en-NG` | *"Show me detergent, milk, and provisions available"* | `SEARCH_PRODUCT`<br>`Keywords: "detergent, milk, provisions"` | *"Here are the top matches for your detergent, milk, and provisions."* |

---

## 5. Local Marketplace Lexicon & Indigenous Taxonomy

A defining differentiator of the N-ATLAS implementation is its **vernacular taxonomy layer**, which understands the everyday informal language of Nigerian commerce:

### 5.1. Indigenous Commodities & Foodstuffs
- **Tubers & Flours:** *Garri Ijebu*, *Garri White*, *Garri Yellow*, *Semo / Semovita*, *Yam flour (Amala / Elubo)*, *Fufu / Akpu*, *Pounded Yam*.
- **Oils & Condiments:** *Epo pupa (Red Palm Oil)*, *Ororo (Vegetable Cooking Oil)*, *Maggi Star*, *Knorr cubes*, *Ogbono*, *Egusi*, *Crayfish*, *Locust beans (Iru / Dawadawa)*.
- **Proteins & Staples:** *Titus fish (Mackerel)*, *Stockfish (Okporoko)*, *Kpomo / Ponmo*, *Shinkafa (Rice)*, *Ewa Oloyin (Honey Beans)*.
- **Packaged Provisions:** *Indomie Hungry Man / Bellefull*, *Gala*, *Maltina*, *Bournvita*, *Milo sachet*, *Peak milk tin/sachet*.

### 5.2. Traditional Trading Measurements
N-ATLAS decodes colloquial volume and packaging units used in open markets:
- **`Derica` / `De-Rica`**: Standard cylindrical tin measure (~0.8 kg) used for rice, beans, and grains.
- **`Mudu`**: Traditional Northern & Middle-belt grain bowl measure (~1.2 - 1.5 kg).
- **`Paint Rubber`**: Standard 4-liter plastic bucket used for measuring garri, beans, and tomatoes.
- **`Kongó`**: Yoruba market dry-volume measure (~1.5 kg).
- **`Sachet` / `Roll` / `Pack`**: Retail micro-portions for milk, detergent, seasonings, and snacks.

---

## 6. Technical Implementation Breakdown

### 6.1. Client-Side Voice Capture (`components/NATLASVoiceAssistant.tsx`)
- **Microphone Stream Acquisition:** Requests browser audio permissions with automated fallback error states for mobile browsers.
- **Dual Visualizer:** Operates a real-time Web Audio API `AnalyserNode` with Fast Fourier Transform (FFT) size 64 to render a reactive audio waveform while the user speaks.
- **Audio Earcons:** Generates procedural audio chimes (`playVoiceTone`) using Web Audio oscillators (440Hz → 880Hz on activation, 587Hz → 880Hz on completion) to provide non-visual feedback for low-literacy users.
- **Dynamic Dialect Selector:** Enables one-tap language switching with native flags, dialect names, and sample phrases.

### 6.2. Audio Protocol & Transport Service (`services/natlasVoiceService.ts`)
- **`blobToBase64()`:** Converts recorded audio blobs into base64 payloads compatible with JSON transmission.
- **`sendNATLASVoiceQuery()`:** Transmits audio payloads along with the current merchant's catalog context (up to 1,500 characters of live inventory) to ground the AI in actual in-stock items.
- **`speakNATLASFeedback()`:** Invokes speech synthesis with Nigerian English/Pidgin acoustic tuning (`en-NG-Neural2-A`), ensuring the spoken output matches the regional accent.

### 6.3. Server-Side N-ATLAS Reasoning Engine (`server.ts` - `/api/natlas/voiceSearch`)
- **Multimodal Audio Processing:** Decodes raw binary audio chunks across `audio/webm`, `audio/mp4`, `audio/ogg`, and `audio/wav`.
- **System Prompt Grounding:** Directs the model with native Nigerian cultural knowledge, phonetics, and marketplace trading rules.
- **Strict Structured JSON Schema:** Enforces type safety for seamless frontend consumption:
  ```json
  {
    "type": "OBJECT",
    "properties": {
      "transcription": { "type": "STRING", "description": "Verbatim speech transcription in native language" },
      "detectedLanguage": { "type": "STRING", "description": "Identified dialect or language" },
      "searchKeywords": { "type": "STRING", "description": "Normalized catalog keywords for database filtering" },
      "categoryFilter": { "type": "STRING", "description": "Target store department or null" },
      "spokenResponse": { "type": "STRING", "description": "Culturally authentic spoken confirmation" },
      "intent": { 
        "type": "STRING", 
        "enum": ["SEARCH_PRODUCT", "FILTER_CATEGORY", "ADD_TO_CART", "GENERAL_QUERY"] 
      }
    },
    "required": ["transcription", "detectedLanguage", "searchKeywords", "spokenResponse", "intent"]
  }
  ```

---

## 7. Application Touchpoints & Accessibility Use Cases

1. **Voice-Powered Storefront Search:**  
   Shoppers tap the glowing N-ATLAS microphone in the search bar. Instead of typing complex spellings, they speak in their mother tongue to instantly filter the store's shelves.
2. **Audio Product & Price Readout:**  
   Users with low literacy or visual impairments can tap the speaker icon on any product card. N-ATLAS speaks the item name, price in Naira, packaging size, and current stock level.
3. **Save-to-Buy Milestone Auditory Notifications:**  
   When a user completes a community savings cycle or lays claim to bulk commodities, N-ATLAS provides spoken audio confirmations including their secure collection PIN.
4. **Hands-Free Rider & Dispatch Verification:**  
   Delivery riders operating motorcycles can receive auditory order details and delivery addresses without needing to look at their screens while navigating traffic.

---

## 8. Alignment with National AI Innovation Challenge Goals

- **Empowering the Grassroots Economy:**  
  Unlocks digital commerce for millions of informal Nigerian traders who were previously excluded by text-heavy, English-only technology.
- **Preservation & Commercialization of Indigenous Languages:**  
  Demonstrates that Yorùbá, Hausa, Igbo, and Nigerian Pidgin are fully capable of powering high-throughput, mission-critical commercial AI systems.
- **Sovereign, Contextualized AI Innovation:**  
  Rather than importing generic foreign chatbots, N-ATLAS solves a uniquely Nigerian socio-economic challenge using deep contextual grounding and local market intelligence.
- **Production-Ready & Scalable:**  
  Fully integrated into Gridstore’s live React and Express stack, validated with real-time Firestore persistence, low-bandwidth optimizations, and sub-second response times.

