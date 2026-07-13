## SECTION 1 — Exam Objectives

**CompTIA Cloud+ CV0-004 · Domain 1.0 (Cloud Architecture) · Objective 1.11**

- Explain the importance of evolving technologies in cloud computing — recognize the emerging technologies that cloud providers now offer as managed building blocks, and understand WHY organizations increasingly consume Machine Learning (ML), Artificial Intelligence (AI), and Internet of Things (IoT) capabilities from the cloud rather than building them from scratch.
- **What the exam expects you to know:**
  - **Machine Learning (ML) / Artificial Intelligence (AI)** — cloud-hosted, pre-trained or trainable intelligence services, including: **Text recognition** (extracting text from documents/images, a.k.a. OCR), **Text translation** (converting language A to language B), **Visual recognition** (identifying objects, faces, and scenes in images/video), **Sentiment analysis** (judging the emotional tone of text), **Voice-to-text** (speech recognition / transcription), **Text-to-voice** (speech synthesis), and **Generative AI** (models that create new text, images, code, or media).
  - **Internet of Things (IoT)** — connecting physical devices to the cloud, including: **Sensors** (devices that measure the physical world), **Gateways** (aggregation/edge devices that bridge sensors to the cloud), **Communication** (how devices exchange messages), and **Transmission protocols** (the lightweight networking standards such as MQTT and HTTP used to move telemetry).
- **Exam tip:** You are NOT expected to build or train these systems. Understand each concept, what problem it solves, and pick the right evolving technology when given a scenario (e.g., "read totals from scanned invoices" → text recognition; "thousands of battery devices reporting temperature" → MQTT over IoT). Think in terms of: consume managed intelligence, connect physical things, and move small messages efficiently.

## SECTION 2 — EXAM KNOWLEDGE

### ML/AI — Text recognition (OCR)
- **Definition:** Text recognition, commonly Optical Character Recognition (OCR), is an AI capability that locates and extracts machine-readable text from images, scanned documents, PDFs, and photos, often preserving structure such as tables and form fields.
- **Why it matters:** Enormous business value is locked inside unstructured paper and image files; OCR turns them into searchable, processable data without manual re-typing, cutting cost and error.
- **Analogy:** A tireless data-entry clerk who can read any scanned page and type its contents perfectly into a spreadsheet in seconds.
- **Example:** An insurance firm uploads 50,000 scanned claim forms; a cloud text-recognition service extracts policy numbers, dates, and totals into a database, replacing weeks of human keying. In the exam, associate OCR with "extract text from images/documents," invoices, receipts, ID cards, and forms processing. It is often paired downstream with sentiment analysis or translation to build a full document pipeline, and it scales elastically because the provider manages the models and compute.

### ML/AI — Text translation
- **Definition:** Text translation is an AI/ML service that automatically converts written text from a source language into one or more target languages while preserving meaning and, ideally, tone.
- **Why it matters:** Global apps must serve users in many languages; machine translation delivers near-instant, low-cost localization of content, support tickets, and product listings that would be impractical to translate by hand.
- **Analogy:** A multilingual interpreter standing beside every user, instantly rewriting any message into their native tongue.
- **Example:** An e-commerce platform auto-translates product reviews written in Japanese and German into English for its US shoppers and vice versa, boosting cross-border sales. For the exam, connect "convert language A to language B" and "real-time or batch localization" to this service. It is frequently chained after voice-to-text (transcribe a call, then translate it) and is distinct from sentiment analysis, which judges tone rather than changing language.

### ML/AI — Visual recognition
- **Definition:** Visual recognition (computer vision) is an AI capability that analyzes images and video to detect and classify objects, scenes, faces, text, activities, and unsafe or inappropriate content.
- **Why it matters:** It automates tasks that previously required human eyes at a scale humans cannot match — moderation, security, quality inspection, and search — enabling real-time decisions on visual data.
- **Analogy:** A security guard with a photographic memory who can watch thousands of camera feeds at once and instantly name everything and everyone in view.
- **Example:** A retailer uses visual recognition to count shelf stock from camera images and flag empty shelves for restocking; a social platform uses it to auto-flag prohibited images. On the exam, map "identify objects/faces/scenes in images or video," "content moderation," and "facial detection" to visual recognition. Distinguish it from text recognition: OCR reads letters, visual recognition understands objects and scenes (though many services do both).

### ML/AI — Sentiment analysis
- **Definition:** Sentiment analysis is a Natural Language Processing (NLP) technique that evaluates text and classifies its emotional tone — typically positive, negative, neutral, or mixed — and can extract key phrases, entities, and intent.
- **Why it matters:** Organizations receive far more free-text feedback (reviews, tweets, support chats) than humans can read; sentiment analysis quantifies customer mood at scale to drive alerts, routing, and product decisions.
- **Analogy:** A mood ring for your inbox that instantly tells you whether each message is happy or angry.
- **Example:** A telecom feeds live support-chat transcripts through sentiment analysis; when a conversation trends strongly negative, it auto-escalates to a senior agent before the customer churns. For the exam, tie "determine emotional tone / positive vs. negative / customer opinion mining" to sentiment analysis. It operates on text, so voice interactions must first pass through voice-to-text; it reports tone rather than translating or extracting text.

### ML/AI — Voice-to-text (speech recognition)
- **Definition:** Voice-to-text, also called Automatic Speech Recognition (ASR) or transcription, is an AI service that converts spoken audio into written text, often with speaker separation, timestamps, and custom vocabularies.
- **Why it matters:** It makes audio searchable and actionable — enabling captions, meeting notes, voice commands, and analytics on call-center recordings — and improves accessibility.
- **Analogy:** A court stenographer who transcribes every spoken word into an accurate written record in real time.
- **Example:** A call center transcribes thousands of daily support calls to text, then runs sentiment analysis and keyword search over the transcripts to spot recurring complaints. On the exam, associate "convert speech/audio to text," "transcription," and "captions" with voice-to-text. Remember the common pipeline: voice-to-text → translation or sentiment analysis. Its mirror image is text-to-voice, which goes the opposite direction.

### ML/AI — Text-to-voice (speech synthesis)
- **Definition:** Text-to-voice, or Text-to-Speech (TTS), is an AI service that converts written text into natural-sounding spoken audio, offering multiple voices, languages, and speaking styles.
- **Why it matters:** It powers accessibility (screen readers), voice assistants, IVR phone menus, audiobooks, and hands-free notifications, delivering human-like speech without recording voice actors.
- **Analogy:** A professional narrator on demand who will read any document aloud in the voice and language you choose.
- **Example:** A news app converts written articles into lifelike audio so commuters can listen hands-free; an airline's system reads gate-change alerts aloud. For the exam, map "convert text into spoken audio," "speech synthesis," and "voice output for assistants/IVR" to text-to-voice. It is the inverse of voice-to-text; the two together create a full conversational voice interface. Modern TTS uses neural models for realistic prosody.

### ML/AI — Generative AI
- **Definition:** Generative AI refers to large models (e.g., Large Language Models and diffusion models) that create brand-new content — text, code, images, audio, or video — in response to natural-language prompts, rather than only classifying existing data.
- **Why it matters:** It dramatically accelerates content creation, coding, summarization, and customer interaction, and cloud providers now offer these powerful foundation models as managed, API-accessible services so businesses need not train them.
- **Analogy:** A creative assistant who can draft, design, summarize, or brainstorm on any topic the instant you describe what you want.
- **Example:** A company uses a cloud generative-AI service to build a customer-support chatbot that writes helpful answers, and to auto-summarize long documents for executives. On the exam, connect "creates new content," "foundation models," "prompts," "chatbots," and "code/image generation" to generative AI. Distinguish it from the earlier discriminative services (recognition, sentiment) which analyze inputs rather than produce novel outputs.

### IoT — Sensors
- **Definition:** Sensors are physical devices that detect and measure real-world conditions — temperature, humidity, motion, light, pressure, GPS location, vibration — and convert them into electrical/digital signals (telemetry) for transmission.
- **Why it matters:** Sensors are the data source of every IoT system; without them there is nothing to monitor, and their small, frequent readings drive automation, analytics, and alerts.
- **Analogy:** The nerve endings of the IoT "body," constantly feeling the environment and reporting sensations back to the brain (the cloud).
- **Example:** A cold-chain logistics company places temperature sensors in refrigerated trucks; each reports every 30 seconds so the cloud can alert drivers before perishable cargo spoils. For the exam, associate sensors with "measure the physical world," "telemetry," and constrained devices with limited power/CPU. Because sensors are numerous and resource-limited, their data is typically sent through gateways using lightweight protocols like MQTT rather than heavy web calls.

### IoT — Gateways
- **Definition:** An IoT gateway is an edge device that aggregates data from many local sensors, performs local filtering/processing (edge computing), handles protocol translation, buffering, and security, and forwards data to the cloud.
- **Why it matters:** Sensors often lack the power, connectivity, or security to reach the cloud directly; gateways bridge local device networks to the internet, reduce bandwidth by pre-processing, and keep working during outages.
- **Analogy:** A regional post office that collects letters from an entire neighborhood, sorts them, and ships one efficient truckload to the central hub instead of each house driving separately.
- **Example:** A factory gateway collects readings from 500 machine sensors over Bluetooth/Zigbee, computes averages locally, and sends only summaries and anomalies to the cloud, slashing bandwidth. On the exam, map gateways to "aggregate sensors," "edge processing," "protocol translation," and "resilience/buffering." Cloud edge runtimes let you run cloud logic and ML models directly on the gateway.

### IoT — Communication
- **Definition:** Communication in IoT is the messaging model by which devices, gateways, and the cloud exchange data — commonly a publish/subscribe pattern through a central broker, plus device state constructs like "shadows" that hold the last known and desired state.
- **Why it matters:** Millions of intermittently connected devices need a scalable, decoupled way to send telemetry and receive commands; pub/sub messaging and state shadows enable reliable two-way control even when devices are offline.
- **Analogy:** A radio station model — devices "publish" on topics like broadcasting, and interested subscribers tune in, without either side needing the other's address.
- **Example:** A smart-thermostat fleet publishes temperature to a broker; the mobile app subscribes for live readings and publishes a "set 21°C" desired state that the device applies when it reconnects. For the exam, connect communication to publish/subscribe, brokers, topics, device shadows/twins, and command-and-control. It is enabled by transmission protocols such as MQTT.

### IoT — Transmission protocols
- **Definition:** Transmission protocols are the standardized network rules that carry IoT messages. The exam-critical ones are **MQTT** (Message Queuing Telemetry Transport), a lightweight publish/subscribe protocol optimized for constrained devices and unreliable networks, and **HTTP/HTTPS**, the standard request/response web protocol usable for less constrained devices or occasional calls.
- **Why it matters:** IoT devices are often battery-powered with limited bandwidth; choosing a lean protocol like MQTT minimizes power and data use while remaining reliable, whereas HTTP is simpler and firewall-friendly but heavier per message.
- **Analogy:** MQTT is like a group text broadcast — tiny, efficient, and delivered to all subscribers; HTTP is like mailing a full formal letter and waiting for a reply each time.
- **Example:** A network of thousands of battery soil-moisture sensors uses MQTT to publish tiny readings hourly for years on one battery, while an occasionally-used vending machine uses HTTPS to post sales. On the exam, pick MQTT for many constrained/low-power devices and pub/sub, and HTTP(S) for simple request/response or web-integrated devices.

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **Scenario 1 — Automated invoice and document processing (Text recognition + Sentiment):** A mid-size accounting firm receives thousands of scanned supplier invoices and emailed complaints daily. It builds a cloud pipeline where a text-recognition (OCR) service extracts vendor, date, and total from each scanned invoice into a database, eliminating manual keying. In parallel, incoming complaint emails are run through sentiment analysis so strongly negative messages jump the support queue. The firm processes documents 20× faster with near-zero data-entry staff, and escalates unhappy clients before they leave.
- **Scenario 2 — Global customer support with voice and translation (Voice-to-text + Translation + Generative AI):** An international SaaS company records all support calls. A voice-to-text service transcribes each call, a translation service normalizes non-English transcripts into English, and a generative-AI model summarizes the conversation and drafts follow-up emails for agents to review. Managers gain searchable, multilingual insight into every interaction, and average handle time drops because agents no longer take manual notes.
- **Scenario 3 — Cold-chain logistics monitoring (Sensors + Gateways + MQTT):** A food distributor equips refrigerated trucks with temperature and door sensors. Each truck has a gateway that aggregates sensor data, buffers it through connectivity dead zones, and publishes readings over MQTT to the cloud IoT platform. If a freezer drifts above threshold, the cloud triggers an alert to the driver and dispatch. Spoilage losses fall sharply and the company can prove compliance to regulators with a complete telemetry log.
- **Scenario 4 — Smart building energy management (Sensors + Gateways + Device shadows):** A commercial landlord installs occupancy, light, and HVAC sensors across floors. Edge gateways run cloud ML models locally to predict occupancy and adjust HVAC in real time even if the internet drops. Device shadows store each thermostat's desired state so facilities staff can set targets from a dashboard that apply once devices reconnect. Energy costs drop 25% while tenant comfort improves.
- **Scenario 5 — Retail media analytics and moderation (Visual recognition + Generative AI + Text-to-voice):** An online marketplace uses visual recognition to auto-tag and moderate millions of seller-uploaded product photos, blocking prohibited items. Generative AI writes SEO product descriptions from the images, and a text-to-voice service produces audio listings for an accessibility feature. Listing throughput rises dramatically while a small trust-and-safety team supervises exceptions.

## SECTION 4 — COMPARISON TABLES

**Table 1 — ML/AI services by input and output**

| Service | Input | Output | Typical use |
|---|---|---|---|
| Text recognition (OCR) | Image/PDF | Extracted text/fields | Invoices, forms, ID scans |
| Text translation | Text (lang A) | Text (lang B) | Localization, cross-border support |
| Visual recognition | Image/video | Labels, objects, faces | Moderation, inspection, security |
| Sentiment analysis | Text | Tone (pos/neg/neutral) | Feedback, social listening |
| Voice-to-text | Audio | Text | Transcription, captions, commands |
| Text-to-voice | Text | Audio | IVR, assistants, accessibility |
| Generative AI | Prompt | New content (text/image/code) | Chatbots, summarization, drafting |

**Table 2 — Discriminative vs. Generative AI**

| Aspect | Discriminative (recognition/sentiment) | Generative AI |
|---|---|---|
| Core function | Classifies/analyzes existing input | Creates new content |
| Example output | "This image = cat" / "negative" | A new essay, image, or code |
| Interaction | Structured input in, label out | Natural-language prompt in, media out |
| Exam keywords | detect, classify, extract, tone | generate, create, foundation model, prompt |

**Table 3 — Voice-to-text vs. Text-to-voice**

| Aspect | Voice-to-text (ASR) | Text-to-voice (TTS) |
|---|---|---|
| Direction | Audio → text | Text → audio |
| Also called | Speech recognition, transcription | Speech synthesis |
| Use case | Captions, meeting notes, commands | IVR, narration, screen readers |
| Common pairing | Feeds translation/sentiment | Reads chatbot/gen-AI output aloud |

**Table 4 — IoT component roles**

| Component | Role | Analogy |
|---|---|---|
| Sensor | Measures physical conditions, emits telemetry | Nerve endings |
| Gateway | Aggregates, filters, translates, buffers, forwards | Regional post office |
| Communication | Pub/sub messaging + device shadows for state | Radio broadcast |
| Transmission protocol | Network rules carrying the messages (MQTT/HTTP) | The delivery road |

**Table 5 — MQTT vs. HTTP for IoT transmission**

| Aspect | MQTT | HTTP/HTTPS |
|---|---|---|
| Model | Publish/subscribe via broker | Request/response |
| Overhead | Very lightweight (small header) | Heavier per message |
| Power/bandwidth | Minimal — ideal for battery/constrained | Higher usage |
| Connectivity | Handles unreliable networks well | Best on stable links |
| Best for | Many low-power devices, frequent telemetry | Simple/occasional calls, web integration |

**Table 6 — Evolving technology to problem it solves**

| Technology | Problem solved | Scenario cue |
|---|---|---|
| Text recognition | Data trapped in scans/images | "extract from scanned invoices" |
| Translation | Language barrier | "serve users in many languages" |
| Sentiment analysis | Too much text feedback to read | "gauge customer mood at scale" |
| Generative AI | Slow manual content/code creation | "chatbot, summarize, generate" |
| IoT sensors + MQTT | Monitoring the physical world cheaply | "thousands of battery devices reporting" |

## SECTION 5 — AWS PROVIDER MAPPING

This section maps the vendor-neutral 1.11 sub-objectives to the AWS-specific services you may see on the exam when a question uses AWS terminology. AWS-only mapping:

- **ML/AI — Text recognition →** Amazon Textract for extracting text, tables, and form fields from scanned documents and PDFs; Amazon Rekognition also detects text within images.
- **ML/AI — Text translation →** Amazon Translate, a managed neural machine-translation service converting text between supported languages in real time or batch.
- **ML/AI — Visual recognition →** Amazon Rekognition for detecting objects, scenes, faces, activities, and unsafe content in images and video.
- **ML/AI — Sentiment analysis →** Amazon Comprehend, the NLP service that determines sentiment (positive/negative/neutral/mixed) plus entities, key phrases, and language.
- **ML/AI — Voice-to-text →** Amazon Transcribe, the automatic speech-recognition service that converts audio/speech into text with speaker labels and timestamps.
- **ML/AI — Text-to-voice →** Amazon Polly, the text-to-speech service that synthesizes lifelike spoken audio in many voices and languages.
- **ML/AI — Generative AI →** Amazon Bedrock (managed access to foundation models via API for text, chat, and images) and Amazon SageMaker (build, train, tune, and deploy custom ML models, including JumpStart foundation models).
- **IoT — Sensors →** AWS IoT Core with device shadows, which stores each sensor device's last-known and desired state so applications can read and command devices even when offline.
- **IoT — Gateways →** AWS IoT Greengrass, the edge runtime that runs on gateway hardware to aggregate local sensors, run Lambda/ML at the edge, buffer data, and sync with the cloud.
- **IoT — Communication →** AWS IoT Core message broker using the MQTT publish/subscribe model and topics, with device shadows for state synchronization and command-and-control.
- **IoT — Transmission protocols →** AWS IoT Core supports MQTT (lightweight pub/sub for constrained devices) and HTTP(S) for device connectivity, with MQTT preferred for low-power, high-scale fleets.

## SECTION 6 — PRACTICE QUESTIONS

1. A firm needs to extract policy numbers and totals from 50,000 scanned insurance forms. Which service fits best?
   - **A.** Sentiment analysis
   - **B.** Text recognition (OCR)
   - **C.** Text-to-voice
   - **D.** Visual recognition

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** OCR or text recognition extracts machine-readable text from scanned documents and forms.

**Why A is wrong:** Sentiment analysis judges tone, it does not extract text from scans.
**Why C is wrong:** Text-to-voice synthesizes speech, the opposite direction.
**Why D is wrong:** Visual recognition detects objects, not printed text fields.

</details>

2. An e-commerce site must show Japanese product reviews to English-speaking shoppers automatically. Which service applies?
   - **A.** Text translation
   - **B.** Voice-to-text
   - **C.** Generative AI
   - **D.** Sensors

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** Text translation converts written text from one language to another.

**Why B is wrong:** Voice-to-text transcribes speech to text, not language conversion.
**Why C is wrong:** Generative AI creates new content, not strict translation.
**Why D is wrong:** Sensors measure the physical world, unrelated to language.

</details>

3. A social platform wants to automatically flag prohibited objects in uploaded photos. Which AI service is correct?
   - **A.** Text recognition
   - **B.** Sentiment analysis
   - **C.** Visual recognition
   - **D.** Text-to-voice

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Visual recognition detects objects, scenes, and unsafe content in uploaded images and video.

**Why A is wrong:** Text recognition reads printed text, not prohibited objects.
**Why B is wrong:** Sentiment analysis judges text tone, not images.
**Why D is wrong:** Text-to-voice synthesizes speech, unrelated to photos.

</details>

4. A support team wants to auto-escalate chats that trend strongly negative. Which capability detects this?
   - **A.** Sentiment analysis
   - **B.** Translation
   - **C.** OCR
   - **D.** Voice-to-text

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** Sentiment analysis classifies the emotional tone of text, flagging strong negative chats.

**Why B is wrong:** Translation changes language, not emotional tone.
**Why C is wrong:** OCR extracts text from images, not tone of chat text.
**Why D is wrong:** Voice-to-text transcribes audio, not chat mood.

</details>

5. A call center needs searchable text from thousands of recorded phone calls. Which service should it use?
   - **A.** Text-to-voice
   - **B.** Voice-to-text
   - **C.** Generative AI
   - **D.** Visual recognition

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Voice-to-text, automatic speech recognition, transcribes spoken audio into text.

**Why A is wrong:** Text-to-voice synthesizes audio, the reverse direction.
**Why C is wrong:** Generative AI creates content, not transcription.
**Why D is wrong:** Visual recognition analyzes images, not audio.

</details>

6. A news app reads written articles aloud in a natural voice for commuters. Which service is this?
   - **A.** Voice-to-text
   - **B.** Sentiment analysis
   - **C.** Text-to-voice
   - **D.** Text recognition

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Text-to-voice, text-to-speech, synthesizes lifelike spoken audio from written text.

**Why A is wrong:** Voice-to-text goes audio to text, the opposite.
**Why B is wrong:** Sentiment analysis judges tone, not narration.
**Why D is wrong:** Text recognition reads scans, not speech.

</details>

7. A company builds a chatbot that writes original answers and summarizes documents from prompts. Which technology?
   - **A.** Discriminative recognition
   - **B.** Generative AI
   - **C.** Sentiment analysis
   - **D.** OCR

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Generative AI creates new content such as answers and summaries from natural-language prompts.

**Why A is wrong:** Discriminative recognition classifies inputs, it does not create.
**Why C is wrong:** Sentiment analysis judges tone, not original writing.
**Why D is wrong:** OCR extracts text, it does not generate answers.

</details>

8. Which statement best distinguishes generative from discriminative AI?
   - **A.** Generative AI only classifies inputs
   - **B.** Discriminative AI creates new media
   - **C.** Generative AI creates new content; discriminative analyzes existing input
   - **D.** They are identical

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Generative AI creates new content, while discriminative AI analyzes existing input.

**Why A is wrong:** Generative does far more than classify; it creates.
**Why B is wrong:** Discriminative analyzes, it does not create media.
**Why D is wrong:** They differ in function, not identical.

</details>

9. In an IoT system, which component directly measures physical conditions like temperature?
   - **A.** Gateway
   - **B.** Sensor
   - **C.** Broker
   - **D.** Device shadow

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Sensors detect and measure real-world conditions such as temperature and emit telemetry.

**Why A is wrong:** A gateway aggregates sensors, it does not measure directly.
**Why C is wrong:** A broker routes messages, it is not a physical sensor.
**Why D is wrong:** A device shadow holds state, not a measurement device.

</details>

10. Which device aggregates data from many local sensors and forwards it to the cloud, often with edge processing?
   - **A.** Sensor
   - **B.** Gateway
   - **C.** Load balancer
   - **D.** Foundation model

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** An IoT gateway aggregates, filters, and forwards sensor data at the edge with processing.

**Why A is wrong:** A sensor measures; it does not aggregate and forward fleets.
**Why C is wrong:** A load balancer distributes traffic, not IoT sensor data.
**Why D is wrong:** A foundation model is ML, not a gateway device.

</details>

11. Thousands of battery-powered sensors must report tiny readings frequently over unreliable networks. Best protocol?
   - **A.** HTTP
   - **B.** FTP
   - **C.** MQTT
   - **D.** SMTP

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** MQTT is a lightweight pub/sub protocol ideal for constrained, low-power, unreliable networks.

**Why A is wrong:** HTTP is heavier per message, poor for tiny frequent battery reports.
**Why B is wrong:** FTP is for file transfer, not telemetry, and heavy.
**Why D is wrong:** SMTP is email, not IoT telemetry.

</details>

12. What IoT construct stores a device's last-known and desired state so commands apply after reconnection?
   - **A.** Device shadow
   - **B.** Sensor array
   - **C.** CDN
   - **D.** IOPS

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** A device shadow or twin holds reported and desired state for offline-tolerant control.

**Why B is wrong:** A sensor array measures, it does not store desired state.
**Why C is wrong:** A CDN caches content, unrelated to device state.
**Why D is wrong:** IOPS is a storage metric, not an IoT construct.

</details>

13. Which messaging pattern is central to IoT communication via a broker?
   - **A.** Request/response only
   - **B.** Publish/subscribe
   - **C.** Point-to-point serial
   - **D.** Batch ETL

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** IoT communication commonly uses publish/subscribe through a message broker.

**Why A is wrong:** It is not request/response only; pub/sub is central.
**Why C is wrong:** Point-to-point serial is not the broker model used.
**Why D is wrong:** Batch ETL is a workflow, not IoT device communication.

</details>

14. A pipeline transcribes calls, then judges caller mood. Which two services in order?
   - **A.** Text-to-voice then OCR
   - **B.** Voice-to-text then sentiment analysis
   - **C.** Translation then visual recognition
   - **D.** Generative AI then sensors

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Transcribe audio to text with voice-to-text, then analyze tone with sentiment analysis.

**Why A is wrong:** Text-to-voice and OCR are the wrong directions for this pipeline.
**Why C is wrong:** Translation then visual recognition does not fit mood detection.
**Why D is wrong:** Generative AI then sensors does not transcribe or gauge mood.

</details>

15. Which AWS service extracts text and form fields from scanned documents?
   - **A.** Amazon Polly
   - **B.** Amazon Textract
   - **C.** Amazon Translate
   - **D.** AWS IoT Core

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Amazon Textract extracts text, tables, and form fields from scanned documents.

**Why A is wrong:** Amazon Polly is text-to-speech, not extraction.
**Why C is wrong:** Amazon Translate is language conversion, not extraction.
**Why D is wrong:** AWS IoT Core is device management, not document extraction.

</details>

16. Which AWS service provides text-to-speech synthesis?
   - **A.** Amazon Transcribe
   - **B.** Amazon Comprehend
   - **C.** Amazon Polly
   - **D.** Amazon Rekognition

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Amazon Polly converts text into lifelike spoken audio, text-to-speech.

**Why A is wrong:** Amazon Transcribe is speech-to-text, the reverse.
**Why B is wrong:** Amazon Comprehend is NLP sentiment, not speech.
**Why D is wrong:** Amazon Rekognition is visual, not speech.

</details>

17. Which AWS service performs sentiment analysis and other NLP tasks?
   - **A.** Amazon Comprehend
   - **B.** Amazon Rekognition
   - **C.** Amazon Bedrock
   - **D.** AWS Greengrass

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** Amazon Comprehend detects sentiment, entities, and key phrases in text.

**Why B is wrong:** Amazon Rekognition is visual recognition, not NLP sentiment.
**Why C is wrong:** Amazon Bedrock is generative AI foundation models, not sentiment.
**Why D is wrong:** AWS Greengrass is edge runtime, not NLP.

</details>

18. Which AWS pairing gives managed access to foundation models and custom model training for generative AI?
   - **A.** Amazon Rekognition and Polly
   - **B.** Amazon Bedrock and SageMaker
   - **C.** Amazon Translate and Transcribe
   - **D.** IoT Core and Greengrass

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Bedrock offers foundation-model APIs and SageMaker builds and trains custom models.

**Why A is wrong:** Rekognition and Polly are vision and speech, not gen-AI training.
**Why C is wrong:** Translate and Transcribe are language services, not gen-AI.
**Why D is wrong:** IoT Core and Greengrass are IoT, not generative AI.

</details>

19. Which AWS edge runtime runs on gateway hardware to process sensor data and ML locally?
   - **A.** AWS IoT Greengrass
   - **B.** Amazon Textract
   - **C.** Amazon Polly
   - **D.** Amazon Comprehend

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** AWS IoT Greengrass runs cloud logic and ML at the edge or gateway hardware.

**Why B is wrong:** Amazon Textract is document extraction, not edge runtime.
**Why C is wrong:** Amazon Polly is speech synthesis, not edge runtime.
**Why D is wrong:** Amazon Comprehend is NLP, not edge runtime.

</details>

20. Which AWS service acts as the MQTT message broker and manages device shadows for IoT communication?
   - **A.** Amazon Rekognition
   - **B.** AWS IoT Core
   - **C.** Amazon SageMaker
   - **D.** Amazon Translate

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** AWS IoT Core provides the MQTT broker, topics, and device shadows for IoT.

**Why A is wrong:** Amazon Rekognition is visual recognition, not IoT broker.
**Why C is wrong:** Amazon SageMaker trains models, not device messaging.
**Why D is wrong:** Amazon Translate converts language, not IoT.

</details>
