### **Functional Requirements**

1. **Input Interface**

   * Design a simple input screen (web-based or Streamlit)
   * Accept **free-text pharmacovigilance narratives** (e.g., adverse event reports, case descriptions)

2. **NLP Processing (STRICT CONSTRAINT)**

   * Use **SpaCy only** (no HuggingFace, no external LLMs)
   * Perform:

     * Named Entity Recognition (NER)
     * Entity normalization (where possible)
     * Relation extraction between entities
   * Focus on PV-relevant entities such as:

     * Drug
     * Adverse Event
     * Indication
     * Patient Characteristics
     * Outcome
     * Dosage (if available)

3. **Structured Output**

   * Convert extracted entities and relations into a **tabular format**
   * Table should include:

     * Entity Type
     * Entity Value
     * Relation Type
     * Source Entity
     * Target Entity
     * Sentence Context

4. **Knowledge Graph Integration**

   * Connect to **Neo4j AuraDB**
   * Define an appropriate **graph schema**:

     * Nodes (e.g., Drug, AdverseEvent, Patient, Indication)
     * Relationships (e.g., CAUSES, TREATS, EXPERIENCED_BY, ASSOCIATED_WITH)
   * Load the extracted data into Neo4j using **Cypher queries**

---

### **Technical Requirements**

Programming Language: **Python**
NLP Library: **SpaCy only**
Graph Database: **Neo4j AuraDB**
Include:

  * Clear project structure
  * Well-commented code
  * Reusable functions
  * Error handling for missing entities

---

### **Expected Deliverables**

1. High-level **architecture diagram (textual description)**
2. End-to-end **Python code**
3. Example pharmacovigilance input text
4. Sample extracted entity–relation table
5. Neo4j **Cypher schema and ingestion queries**
6. Brief explanation of assumptions and limitations

---

### **Important Constraints**

Do NOT use LLM-based NLP models
Do NOT use rule engines outside SpaCy
Ensure the solution is **pharmacovigilance-focused**
Keep the design modular and production-oriented