# Chapter 1: Introduction

Phishing is one of the most effective social engineering methods that hackers use to get personal information, credentials, and gain access to an organization. Phishing communications are sent over email, text, QR codes, or phone calls. They usually try to get you to open a link or attachment that might lead to a credential harvesting form or malware. Spam is becoming the most widespread type of cybercrime, with 3.4 billion emails sent every day (Ronish et al., 2025). According to the FBI, phishing is the most common Internet crime, with 54 million dollars lost in 2020.

Phishing emails are a big problem for businesses all over the world. These emails get into people's inboxes and look like emails from well-known brands, using realistic themes to trick people into following their instructions. The phishing problem is getting worse, especially since more and more businesses are moving their hosting to the cloud, which makes it easier for hackers to find their domain (Dutta, 2024). As AI services have become more accessible and popular, scammers have started using AI to make high-quality phishing emails that look real and make it more likely that they will get their victims. As large language models get better, they can get rid of common grammar and spelling mistakes, making phishing emails harder to distinguish from legitimate ones (AI and Fraud: How to Protect Yourself from Fake Videos and Phishing Emails, 2024).

Modern email services have added advanced malicious email filters and authentication methods to protect against these threats by spotting suspicious senders and looking for signs of phishing in email content (Takashi et al., 2024). Even so, phishing emails are still being missed and misclassified. Many email systems may label emails as possibly fraudulent without giving users a clear reason for doing so. As a result, people who receive emails can't tell if they're really phishing, which means they might miss an important message or accidentally click on a bad link. To avoid falling victim to this threat, organizations need to adeptly detect and prevent these attacks at scale by adopting effective phishing detection techniques. Leveraging systems that use LLM embeddings can significantly bolster these efforts (Basit, Zafar, Liu, Javed, Jalil, and Kifayat, 2021; Sarker, 2023).

---

## 1.1 Problem Background

Numerous studies have been presented to bolster defenses against phishing emails through the application of machine learning techniques for the analysis and classification of emails. These algorithms statistically derive phishing-specific characteristics from headers and bodies for binary categorization (ham/spam) (Beaman et al., 2022; Mughaid et al., 2022; Nabeel et al., 2021; Sonowal et al., 2020). Furthermore, techniques employing natural language processing to analyze the content of phishing emails and discern their context and intent have been investigated (Che et al., 2017; Li et al., 2022). The methods encompass basic frequency-based feature vector creation, like TF-IDF and Bag-of-Words, as well as sophisticated deep learning models like BERT (Bidirectional Encoder Representations from Transformers), which employ semantic analysis to identify social engineering procedures.

Modern machine learning techniques categorize features obtained from sender data, URLs, email content, headers, and stylometric patterns (Jovanovic et al., 2024; Ilias et al., 2024; Salloum et al., 2022). Hybrid feature selection, which combines a variety of different methods, is often used to make detection more accurate and faster (Hamid et al., 2011; Naveen et al., 2022). Some machine learning-based methods work better on current phishing email datasets, but they mostly give statistical detection results without providing clear explanations. Additionally, models trained on limited datasets may perform poorly when faced with a variety of phishing emails in multiple languages. Some of the problems that ML/DL models have are overfitting to known data, inability to adapt to new attack patterns, and the need to be retrained often (Kyaw et al., 2024).

Large Language Models (LLMs) are advanced AI systems that use deep learning and large amounts of text to learn and produce natural language. Their widespread use is driven by their ability to understand natural language, perform a variety of language-related tasks, and boost productivity and efficiency across applications (Nam et al., 2024). Leveraging extensive textual data and transformer architectures, LLMs like OpenAI's GPT-4 excel in natural language processing tasks, including the analysis of intricate language patterns and anomaly detection (Hua et al., 2024; Qin et al., 2024). In the realm of source code vulnerability datasets, LLMs can proficiently comprehend data by correlating tokens and recognizing patterns (Giner-Miguelez et al., 2024). Their capacity to comprehend context, semantics, and subtle linguistic signals enables them to detect phishing emails that might evade standard filters (Jiang et al., 2024).

The model's decision-making processes are difficult to explain, making LLMs challenging for classification tasks (Luo & Specia, 2024). LLMs are often considered "black boxes," which can hinder AI solution development due to their complexity. AI transparency, including data use and model decision processes, is essential for vulnerability detection, where understanding decision rationale can improve security.

---

## 1.2 Research Aim and Objectives

This study aims to design and assess a phishing email detection system that employs Large Language Model (LLM) embeddings to improve the accuracy and reliability of phishing email identification. The study highlights the importance of semantic comprehension of email content to enhance user awareness and responses to phishing threats, surpassing conventional machine learning detection techniques.

### Objectives

- To assess the existing literature on the application of Large Language Models in cybersecurity, particularly concerning the analysis of email threats and the identification of phishing attempts.

- To assemble and categorize a labeled dataset of authentic and phishing emails from publicly available sources for model training and assessment.

- To compare and analyze various LLM embedding techniques, including BERT, RoBERTa, and DistilBERT, to ascertain the most effective method for representing email text.

- To employ the selected embedding technique to train a machine learning classifier and assess its ability to accurately distinguish between authentic and phishing emails.

- To evaluate the efficacy of the proposed detection system using standard metrics, such as precision, recall, F1-score, and accuracy.

- To assess the effectiveness of the proposed LLM-based method in improving phishing detection performance and its practical applicability, relative to conventional techniques.
