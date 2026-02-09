# Design Document: Pharmacovigilance NLP Knowledge Graph

## Overview

The Pharmacovigilance NLP Knowledge Graph system is a Python-based application that processes free-text adverse event reports using SpaCy for natural language processing and stores extracted entities and relationships in a Neo4j AuraDB knowledge graph. The system follows a modular architecture with clear separation between the user interface, NLP processing, and data persistence layers.

The application accepts unstructured pharmacovigilance narratives through a Streamlit web interface, extracts relevant entities (drugs, adverse events, indications, patient characteristics, outcomes, dosages) and their relationships using SpaCy's NER and dependency parsing capabilities, presents results in a tabular format, and persists the knowledge graph in Neo4j for complex querying and pattern analysis.

## Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Streamlit Web Interface                  │
│                    (Input & Visualization)                   │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│                    NLP Processing Layer                      │
│  ┌──────────────────┐      ┌──────────────────────────┐    │
│  │ Entity Extractor │      │  Relation Extractor      │    │
│  │   (SpaCy NER +   │─────▶│  (SpaCy Dependency       │    │
│  │   EntityRuler)   │      │   Parser + Matcher)      │    │
│  └──────────────────┘      └──────────────────────────┘    │
│           │                            │                     │
│           └────────────┬───────────────┘                     │
│                        ▼                                     │
│              ┌──────────────────┐                           │
│              │ Entity Normalizer│                           │
│              └──────────────────┘                           │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│                  Data Persistence Layer                      │
│  ┌──────────────────┐      ┌──────────────────────────┐    │
│  │ Structured Output│      │   Neo4j Graph Store      │    │
│  │  (Pandas DataFrame)│    │   (AuraDB Connection)    │    │
│  └──────────────────┘      └──────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
```

### Component Responsibilities

1. **Streamlit Web Interface**: Handles user input, displays processing status, renders tabular output
2. **Entity Extractor**: Uses SpaCy's NER pipeline with custom EntityRuler for PV-specific entities
3. **Relation Extractor**: Leverages SpaCy's dependency parser and Matcher to identify semantic relationships
4. **Entity Normalizer**: Applies case normalization and basic text cleaning
5. **Structured Output**: Converts extracted data to pandas DataFrame for display
6. **Neo4j Graph Store**: Manages connection to AuraDB and executes Cypher queries for data ingestion

## Components and Interfaces

### 1. Streamlit Web Interface

**Purpose**: Provide user-facing input/output interface

**Key Functions**:
- `render_input_form()`: Display text area for PV narrative input
- `display_results(df: pd.DataFrame)`: Render extraction results as table
- `show_error(message: str)`: Display error messages to user
- `show_processing_status()`: Show loading spinner during processing

**Interface**:
```python
def process_narrative(text: str) -> Tuple[pd.DataFrame, bool]:
    """
    Process PV narrative and return results.
    
    Args:
        text: Free-text pharmacovigilance narrative
        
    Returns:
        Tuple of (results_dataframe, success_flag)
    """
```

### 2. Entity Extractor

**Purpose**: Extract pharmacovigilance-relevant entities from text

**Key Functions**:
- `initialize_nlp_pipeline()`: Load SpaCy model and configure EntityRuler
- `extract_entities(doc: spacy.tokens.Doc) -> List[Entity]`: Extract all entities from processed document
- `get_sentence_context(entity: spacy.tokens.Span) -> str`: Retrieve sentence containing entity

**Entity Class**:
```python
@dataclass
class Entity:
    text: str
    label: str  # DRUG, ADVERSE_EVENT, INDICATION, PATIENT_CHAR, OUTCOME, DOSAGE
    start_char: int
    end_char: int
    sentence_context: str
    normalized_text: str
```

**SpaCy Configuration**:
- Base model: `en_core_web_sm` or `en_core_web_md`
- Custom EntityRuler with PV-specific patterns for:
  - DRUG: Common drug names, generic/brand names
  - ADVERSE_EVENT: Medical conditions, symptoms, adverse reactions
  - INDICATION: Diseases, conditions being treated
  - PATIENT_CHAR: Age, gender, medical history descriptors
  - OUTCOME: Recovery, death, hospitalization, ongoing
  - DOSAGE: Dose amounts, frequencies, routes of administration

**Interface**:
```python
def extract_entities(text: str) -> List[Entity]:
    """
    Extract PV entities from narrative text.
    
    Args:
        text: Input PV narrative
        
    Returns:
        List of extracted Entity objects
    """
```

### 3. Relation Extractor

**Purpose**: Identify semantic relationships between extracted entities

**Key Functions**:
- `extract_relations(doc: spacy.tokens.Doc, entities: List[Entity]) -> List[Relation]`: Extract relationships using dependency parsing
- `identify_causation(token1, token2) -> bool`: Detect CAUSES relationships
- `identify_treatment(token1, token2) -> bool`: Detect TREATS relationships
- `identify_experience(token1, token2) -> bool`: Detect EXPERIENCED_BY relationships

**Relation Class**:
```python
@dataclass
class Relation:
    relation_type: str  # CAUSES, TREATS, EXPERIENCED_BY, ASSOCIATED_WITH
    source_entity: Entity
    target_entity: Entity
    confidence: float
```

**Relation Extraction Strategy**:
- Use SpaCy's dependency parser to analyze syntactic structure
- Apply pattern matching rules based on dependency paths:
  - CAUSES: Drug → (nsubj/agent) → verb (cause/induce/trigger) → (dobj) → Adverse Event
  - TREATS: Drug → (nsubj) → verb (treat/manage/control) → (dobj) → Indication
  - EXPERIENCED_BY: Adverse Event → (nsubjpass) → verb (experience/suffer/develop) → (agent) → Patient
  - ASSOCIATED_WITH: Entity → (prep/conj) → Entity (within same sentence)
- Use SpaCy's Matcher for pattern-based relation detection

**Interface**:
```python
def extract_relations(text: str, entities: List[Entity]) -> List[Relation]:
    """
    Extract relationships between entities.
    
    Args:
        text: Input PV narrative
        entities: Previously extracted entities
        
    Returns:
        List of Relation objects
    """
```

### 4. Entity Normalizer

**Purpose**: Normalize entity text for consistency

**Key Functions**:
- `normalize_entity(entity: Entity) -> str`: Apply normalization rules
- `normalize_case(text: str) -> str`: Convert to lowercase with proper noun handling
- `remove_extra_whitespace(text: str) -> str`: Clean whitespace

**Normalization Rules**:
- Convert to lowercase (except for acronyms)
- Remove leading/trailing whitespace
- Collapse multiple spaces to single space
- Preserve hyphenated terms
- Handle common abbreviations (mg, ml, etc.)

**Interface**:
```python
def normalize_entity(entity: Entity) -> Entity:
    """
    Normalize entity text.
    
    Args:
        entity: Entity to normalize
        
    Returns:
        Entity with normalized_text field populated
    """
```

### 5. Structured Output Generator

**Purpose**: Convert extracted data to tabular format

**Key Functions**:
- `create_output_dataframe(entities: List[Entity], relations: List[Relation]) -> pd.DataFrame`: Generate results table
- `format_entity_row(entity: Entity) -> Dict`: Convert entity to table row
- `format_relation_row(relation: Relation) -> Dict`: Convert relation to table row

**Output Schema**:
```python
{
    'Entity Type': str,        # DRUG, ADVERSE_EVENT, etc.
    'Entity Value': str,       # Normalized entity text
    'Relation Type': str,      # CAUSES, TREATS, etc. (or empty)
    'Source Entity': str,      # Source entity text (or empty)
    'Target Entity': str,      # Target entity text (or empty)
    'Sentence Context': str    # Full sentence containing entity/relation
}
```

**Interface**:
```python
def create_structured_output(entities: List[Entity], relations: List[Relation]) -> pd.DataFrame:
    """
    Create tabular output from extracted data.
    
    Args:
        entities: Extracted entities
        relations: Extracted relations
        
    Returns:
        DataFrame with structured output
    """
```

### 6. Neo4j Graph Store

**Purpose**: Persist entities and relationships in knowledge graph

**Key Functions**:
- `connect_to_neo4j(uri: str, username: str, password: str) -> neo4j.Driver`: Establish connection
- `create_schema()`: Initialize graph schema with constraints and indexes
- `ingest_entities(entities: List[Entity])`: Create nodes in graph
- `ingest_relations(relations: List[Relation])`: Create relationships in graph
- `close_connection()`: Clean up database connection

**Graph Schema**:

Nodes:
- `Drug`: Properties: {name: str, normalized_name: str, mention_count: int}
- `AdverseEvent`: Properties: {name: str, normalized_name: str, severity: str}
- `Indication`: Properties: {name: str, normalized_name: str}
- `Patient`: Properties: {characteristics: str, age: str, gender: str}
- `Outcome`: Properties: {type: str, description: str}

Relationships:
- `(Drug)-[CAUSES]->(AdverseEvent)`: Properties: {confidence: float, source_text: str}
- `(Drug)-[TREATS]->(Indication)`: Properties: {confidence: float, source_text: str}
- `(Patient)-[EXPERIENCED]->(AdverseEvent)`: Properties: {timestamp: str, context: str}
- `(Entity)-[ASSOCIATED_WITH]->(Entity)`: Properties: {context: str}

**Cypher Queries**:

Schema Creation:
```cypher
// Create constraints
CREATE CONSTRAINT drug_name IF NOT EXISTS FOR (d:Drug) REQUIRE d.normalized_name IS UNIQUE;
CREATE CONSTRAINT ae_name IF NOT EXISTS FOR (ae:AdverseEvent) REQUIRE ae.normalized_name IS UNIQUE;
CREATE CONSTRAINT indication_name IF NOT EXISTS FOR (i:Indication) REQUIRE i.normalized_name IS UNIQUE;

// Create indexes
CREATE INDEX drug_name_idx IF NOT EXISTS FOR (d:Drug) ON (d.name);
CREATE INDEX ae_name_idx IF NOT EXISTS FOR (ae:AdverseEvent) ON (ae.name);
```

Entity Ingestion:
```cypher
// Merge Drug node
MERGE (d:Drug {normalized_name: $normalized_name})
ON CREATE SET d.name = $name, d.mention_count = 1
ON MATCH SET d.mention_count = d.mention_count + 1
RETURN d;

// Merge AdverseEvent node
MERGE (ae:AdverseEvent {normalized_name: $normalized_name})
ON CREATE SET ae.name = $name
RETURN ae;
```

Relationship Ingestion:
```cypher
// Create CAUSES relationship
MATCH (d:Drug {normalized_name: $drug_name})
MATCH (ae:AdverseEvent {normalized_name: $ae_name})
MERGE (d)-[r:CAUSES]->(ae)
ON CREATE SET r.confidence = $confidence, r.source_text = $context
RETURN r;
```

**Interface**:
```python
class Neo4jGraphStore:
    def __init__(self, uri: str, username: str, password: str):
        """Initialize connection to Neo4j AuraDB."""
        
    def create_schema(self) -> None:
        """Create graph schema with constraints and indexes."""
        
    def ingest_entity(self, entity: Entity) -> None:
        """Create or update entity node in graph."""
        
    def ingest_relation(self, relation: Relation) -> None:
        """Create relationship in graph."""
        
    def close(self) -> None:
        """Close database connection."""
```

## Data Models

### Entity Data Model

```python
from dataclasses import dataclass
from typing import Optional

@dataclass
class Entity:
    """Represents an extracted pharmacovigilance entity."""
    text: str                    # Original text span
    label: str                   # Entity type (DRUG, ADVERSE_EVENT, etc.)
    start_char: int             # Start position in document
    end_char: int               # End position in document
    sentence_context: str       # Full sentence containing entity
    normalized_text: str        # Normalized entity value
    
    def to_dict(self) -> dict:
        """Convert to dictionary for DataFrame creation."""
        return {
            'Entity Type': self.label,
            'Entity Value': self.normalized_text,
            'Relation Type': '',
            'Source Entity': '',
            'Target Entity': '',
            'Sentence Context': self.sentence_context
        }
```

### Relation Data Model

```python
from dataclasses import dataclass
from typing import Optional

@dataclass
class Relation:
    """Represents a relationship between two entities."""
    relation_type: str          # CAUSES, TREATS, EXPERIENCED_BY, ASSOCIATED_WITH
    source_entity: Entity       # Source entity in relationship
    target_entity: Entity       # Target entity in relationship
    confidence: float           # Confidence score (0.0 to 1.0)
    sentence_context: str       # Sentence containing the relationship
    
    def to_dict(self) -> dict:
        """Convert to dictionary for DataFrame creation."""
        return {
            'Entity Type': '',
            'Entity Value': '',
            'Relation Type': self.relation_type,
            'Source Entity': self.source_entity.normalized_text,
            'Target Entity': self.target_entity.normalized_text,
            'Sentence Context': self.sentence_context
        }
```

### Configuration Data Model

```python
from dataclasses import dataclass
from typing import Optional

@dataclass
class AppConfig:
    """Application configuration."""
    # SpaCy configuration
    spacy_model: str = "en_core_web_sm"
    entity_patterns_file: str = "entity_patterns.jsonl"
    
    # Neo4j configuration
    neo4j_uri: str
    neo4j_username: str
    neo4j_password: str
    
    # Processing configuration
    max_text_length: int = 10000
    min_confidence_threshold: float = 0.5
    
    # Logging configuration
    log_level: str = "INFO"
    log_file: str = "pv_nlp.log"
```

## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system—essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*


### Property 1: Text Input Acceptance
*For any* non-empty string input, the Input_Interface should accept it without raising an exception and pass it to the NLP_Processor.
**Validates: Requirements 1.1, 1.2**

### Property 2: Multi-line Text Support
*For any* text containing multiple sentences and paragraphs (with newline characters), the system should process it without data loss or corruption.
**Validates: Requirements 1.3**

### Property 3: Entity Context Preservation
*For any* extracted entity, the sentence context field should contain the complete sentence in which the entity appears.
**Validates: Requirements 2.9**

### Property 4: Entity Normalization Completeness
*For any* extracted entity, the normalized_text field should be populated with either the normalized value or the original text if normalization fails.
**Validates: Requirements 3.1, 3.2**

### Property 5: Case-Insensitive Normalization
*For any* entity text, normalizing it in different cases (uppercase, lowercase, mixed case) should produce the same normalized result.
**Validates: Requirements 3.3**

### Property 6: Relationship Source and Target Capture
*For any* extracted relationship, both the source_entity and target_entity fields should be populated with valid Entity objects.
**Validates: Requirements 4.8**

### Property 7: Structured Output Format
*For any* processing result, the output should be a tabular structure (DataFrame) with exactly these columns: Entity Type, Entity Value, Relation Type, Source Entity, Target Entity, Sentence Context.
**Validates: Requirements 5.1, 5.2, 5.3, 5.4, 5.5, 5.6, 5.7**

### Property 8: Output Display Integration
*For any* structured output generated, it should be passed to the Input_Interface display function without modification.
**Validates: Requirements 5.9**

### Property 9: Entity-to-Node Round Trip
*For any* extracted entity that is ingested into Neo4j, querying the graph for that entity's normalized name should return a node with matching properties.
**Validates: Requirements 6.10**

### Property 10: Relationship-to-Edge Round Trip
*For any* extracted relationship that is ingested into Neo4j, querying the graph for relationships between the source and target entities should return an edge with matching relationship type.
**Validates: Requirements 6.11**

### Property 11: Error Logging Completeness
*For any* error that occurs during NLP processing or database operations, an error log entry should be created with error details.
**Validates: Requirements 7.1, 7.5**

### Property 12: Error Message Display
*For any* error that occurs during processing, a user-friendly error message should be displayed through the Input_Interface.
**Validates: Requirements 7.2**

### Property 13: System Stability After Errors
*For any* error that occurs during processing, the system should remain operational and accept subsequent processing requests without requiring restart.
**Validates: Requirements 7.6**

### Property 14: Input Validation Before Processing
*For any* input text, validation should be performed before passing it to the NLP_Processor.
**Validates: Requirements 10.3**

### Property 15: Processing Activity Logging
*For any* processing operation (entity extraction, relation extraction, graph ingestion), at least one log entry should be created.
**Validates: Requirements 10.5**

## Error Handling

### Error Categories

1. **Input Validation Errors**
   - Empty input text
   - Text exceeding maximum length
   - Invalid character encoding

2. **NLP Processing Errors**
   - SpaCy model loading failure
   - Entity extraction failure
   - Relation extraction failure
   - Normalization errors

3. **Database Errors**
   - Connection failure to Neo4j AuraDB
   - Authentication errors
   - Cypher query execution errors
   - Transaction failures
   - Network timeouts

4. **Data Errors**
   - Missing required entity fields
   - Invalid entity types
   - Malformed relationship data

### Error Handling Strategy

**Input Validation Layer**:
```python
def validate_input(text: str) -> Tuple[bool, Optional[str]]:
    """
    Validate input text before processing.
    
    Returns:
        Tuple of (is_valid, error_message)
    """
    if not text or text.strip() == "":
        return False, "Input text cannot be empty"
    
    if len(text) > MAX_TEXT_LENGTH:
        return False, f"Input text exceeds maximum length of {MAX_TEXT_LENGTH} characters"
    
    try:
        text.encode('utf-8')
    except UnicodeEncodeError:
        return False, "Input contains invalid characters"
    
    return True, None
```

**NLP Processing Layer**:
```python
def extract_entities_safe(text: str) -> Tuple[List[Entity], Optional[str]]:
    """
    Extract entities with error handling.
    
    Returns:
        Tuple of (entities_list, error_message)
    """
    try:
        doc = nlp(text)
        entities = extract_entities(doc)
        return entities, None
    except Exception as e:
        logger.error(f"Entity extraction failed: {str(e)}", exc_info=True)
        return [], f"Entity extraction failed: {str(e)}"
```

**Database Layer**:
```python
def ingest_with_retry(entity: Entity, max_retries: int = 3) -> bool:
    """
    Ingest entity with retry logic.
    
    Returns:
        True if successful, False otherwise
    """
    for attempt in range(max_retries):
        try:
            with driver.session() as session:
                session.write_transaction(create_entity_node, entity)
            return True
        except Neo4jError as e:
            logger.warning(f"Ingestion attempt {attempt + 1} failed: {str(e)}")
            if attempt == max_retries - 1:
                logger.error(f"Failed to ingest entity after {max_retries} attempts")
                return False
            time.sleep(2 ** attempt)  # Exponential backoff
    return False
```

**User-Facing Error Messages**:
- Input errors: "Please enter a valid pharmacovigilance narrative"
- Processing errors: "Unable to process the narrative. Please try again or contact support"
- Database errors: "Unable to save results to knowledge graph. Results are displayed below"
- No entities found: "No pharmacovigilance entities detected in the provided text"

### Logging Configuration

```python
import logging

logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('pv_nlp.log'),
        logging.StreamHandler()
    ]
)

logger = logging.getLogger('pv_nlp_system')
```

**Log Levels**:
- DEBUG: Detailed processing information, entity/relation details
- INFO: Processing milestones (started, completed), entity counts
- WARNING: Recoverable errors, retry attempts, missing optional data
- ERROR: Processing failures, database errors, validation failures
- CRITICAL: System-level failures, configuration errors

## Testing Strategy

### Dual Testing Approach

The system will employ both unit testing and property-based testing to ensure comprehensive coverage:

**Unit Tests**: Focus on specific examples, edge cases, and integration points
- Test specific PV narratives with known entities and relationships
- Test error conditions (empty input, connection failures, invalid data)
- Test integration between components
- Test specific entity types (drug names, adverse events, etc.)
- Test specific relationship types (CAUSES, TREATS, etc.)

**Property-Based Tests**: Verify universal properties across all inputs
- Test that all extracted entities have sentence context
- Test that normalization always produces a result
- Test that output always has required columns
- Test that errors are always logged
- Test that the system remains stable after errors

### Property-Based Testing Configuration

**Library**: Use `hypothesis` for Python property-based testing

**Configuration**:
- Minimum 100 iterations per property test
- Each test tagged with feature name and property number
- Tag format: `# Feature: pv-nlp-knowledge-graph, Property {N}: {property_text}`

**Example Property Test**:
```python
from hypothesis import given, strategies as st
import pytest

# Feature: pv-nlp-knowledge-graph, Property 3: Entity Context Preservation
@given(st.text(min_size=10, max_size=1000))
@pytest.mark.property_test
def test_entity_context_preservation(narrative_text):
    """
    Property: For any extracted entity, the sentence context field 
    should contain the complete sentence in which the entity appears.
    """
    entities = extract_entities(narrative_text)
    
    for entity in entities:
        # Verify sentence context is not empty
        assert entity.sentence_context, f"Entity {entity.text} missing sentence context"
        
        # Verify entity text appears in sentence context
        assert entity.text in entity.sentence_context, \
            f"Entity text '{entity.text}' not found in context '{entity.sentence_context}'"
```

### Unit Testing Strategy

**Test Organization**:
```
tests/
├── unit/
│   ├── test_entity_extractor.py
│   ├── test_relation_extractor.py
│   ├── test_normalizer.py
│   ├── test_output_generator.py
│   └── test_neo4j_store.py
├── integration/
│   ├── test_end_to_end.py
│   └── test_neo4j_integration.py
├── property/
│   ├── test_entity_properties.py
│   ├── test_relation_properties.py
│   └── test_system_properties.py
└── fixtures/
    ├── sample_narratives.py
    └── expected_outputs.py
```

**Example Unit Tests**:

```python
def test_extract_drug_entities():
    """Test extraction of drug entities from narrative."""
    narrative = "Patient was prescribed aspirin 100mg daily for cardiovascular protection."
    entities = extract_entities(narrative)
    
    drug_entities = [e for e in entities if e.label == "DRUG"]
    assert len(drug_entities) >= 1
    assert any("aspirin" in e.text.lower() for e in drug_entities)

def test_extract_causes_relationship():
    """Test extraction of CAUSES relationship."""
    narrative = "Ibuprofen caused severe gastric bleeding in the patient."
    entities = extract_entities(narrative)
    relations = extract_relations(narrative, entities)
    
    causes_relations = [r for r in relations if r.relation_type == "CAUSES"]
    assert len(causes_relations) >= 1
    
    relation = causes_relations[0]
    assert relation.source_entity.label == "DRUG"
    assert relation.target_entity.label == "ADVERSE_EVENT"

def test_empty_input_handling():
    """Test that empty input is handled gracefully."""
    with pytest.raises(ValueError, match="Input text cannot be empty"):
        process_narrative("")

def test_neo4j_connection_failure():
    """Test handling of Neo4j connection failure."""
    with patch('neo4j.GraphDatabase.driver') as mock_driver:
        mock_driver.side_effect = ServiceUnavailable("Connection failed")
        
        result = ingest_entities([sample_entity])
        assert result is False
        # Verify error was logged
        assert "Connection failed" in caplog.text
```

### Test Coverage Goals

- Unit test coverage: Minimum 80% code coverage
- Property test coverage: All 15 correctness properties implemented
- Integration test coverage: All component interfaces tested
- Edge case coverage: All identified edge cases tested

### Continuous Testing

- Run unit tests on every commit
- Run property tests (100 iterations) on every pull request
- Run full property tests (1000 iterations) nightly
- Run integration tests against test Neo4j instance before deployment

## Assumptions and Limitations

### Assumptions

1. **Input Language**: All PV narratives are in English
2. **Text Format**: Input is plain text (no HTML, PDF, or other formats)
3. **Entity Patterns**: Common drug names and adverse events follow standard medical terminology
4. **Sentence Boundaries**: SpaCy's sentence segmentation is accurate for medical text
5. **Neo4j Availability**: Neo4j AuraDB instance is accessible and properly configured
6. **Network Connectivity**: Stable network connection for database operations
7. **Text Length**: PV narratives are typically under 10,000 characters
8. **Entity Disambiguation**: Entities with same text but different meanings are not disambiguated

### Limitations

1. **NLP Accuracy**: Entity and relation extraction accuracy depends on SpaCy's pre-trained models and custom patterns
   - Expected entity extraction F1 score: 70-85% (depends on entity type)
   - Expected relation extraction F1 score: 60-75% (depends on relation type)

2. **Entity Coverage**: Only recognizes six entity types (Drug, Adverse Event, Indication, Patient Characteristic, Outcome, Dosage)
   - Does not extract: lab values, procedures, anatomical locations, temporal information

3. **Relation Types**: Limited to four relationship types (CAUSES, TREATS, EXPERIENCED_BY, ASSOCIATED_WITH)
   - Does not capture: temporal relationships, conditional relationships, negations

4. **Normalization**: Basic text normalization only
   - No medical ontology mapping (e.g., to MedDRA, SNOMED CT)
   - No drug name standardization to generic/brand equivalents
   - No spelling correction

5. **Context Window**: Relationships only detected within single sentences
   - Cross-sentence relationships not captured
   - Coreference resolution not performed

6. **Scalability**: Single-threaded processing
   - No batch processing support
   - No parallel processing of multiple narratives

7. **Data Persistence**: No deduplication in Neo4j
   - Multiple mentions of same entity create separate nodes (with merge logic)
   - No entity resolution across different narratives

8. **Security**: Basic security considerations
   - No PHI/PII detection or redaction
   - No access control or audit logging
   - Assumes trusted input source

9. **Validation**: No medical validation
   - Does not verify drug-adverse event associations against known databases
   - Does not flag implausible relationships
   - No severity assessment

10. **SpaCy Model Dependency**: Performance limited by SpaCy's pre-trained models
    - May require custom training for optimal PV performance
    - Entity patterns need manual curation and maintenance

### Future Enhancements

1. Integration with medical ontologies (MedDRA, SNOMED CT, RxNorm)
2. Cross-sentence relationship extraction
3. Temporal information extraction
4. Negation detection
5. Batch processing support
6. Custom SpaCy model training on PV corpus
7. Entity disambiguation
8. Confidence scoring for extractions
9. PHI/PII detection and redaction
10. Integration with pharmacovigilance databases for validation
