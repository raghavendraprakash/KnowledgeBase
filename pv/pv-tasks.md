# Implementation Plan: Pharmacovigilance NLP Knowledge Graph

## Overview

This implementation plan breaks down the PV NLP Knowledge Graph system into discrete coding tasks. The system will be built incrementally, starting with core data models, then NLP processing components, followed by the user interface and Neo4j integration. Each major component includes corresponding tests to validate functionality early.

The implementation follows a modular architecture with clear separation between the Streamlit interface, SpaCy-based NLP processing, and Neo4j graph storage. Testing includes both unit tests for specific examples and property-based tests for universal correctness properties.

## Tasks

- [ ] 1. Set up project structure and dependencies
  - Create directory structure: `src/`, `tests/unit/`, `tests/property/`, `tests/integration/`, `tests/fixtures/`
  - Create `requirements.txt` with dependencies: spacy, streamlit, neo4j, pandas, hypothesis, pytest
  - Create `README.md` with setup instructions
  - Create `.env.example` for configuration template
  - Create `config.py` for configuration management
  - _Requirements: 10.1, 10.6_

- [ ] 2. Implement core data models
  - [ ] 2.1 Create Entity and Relation data classes
    - Implement `Entity` dataclass with fields: text, label, start_char, end_char, sentence_context, normalized_text
    - Implement `Relation` dataclass with fields: relation_type, source_entity, target_entity, confidence, sentence_context
    - Implement `to_dict()` methods for DataFrame conversion
    - Implement `AppConfig` dataclass for application configuration
    - _Requirements: 2.3, 2.4, 2.5, 2.6, 2.7, 2.8, 4.4, 4.5, 4.6, 4.7_

  - [ ]* 2.2 Write unit tests for data models
    - Test Entity creation and to_dict() conversion
    - Test Relation creation and to_dict() conversion
    - Test edge cases (empty strings, None values)
    - _Requirements: 2.3, 2.4, 2.5, 2.6, 2.7, 2.8_

- [ ] 3. Implement entity extraction component
  - [ ] 3.1 Create EntityExtractor class with SpaCy integration
    - Initialize SpaCy pipeline with `en_core_web_sm` model
    - Create `entity_patterns.jsonl` with PV-specific entity patterns for DRUG, ADVERSE_EVENT, INDICATION, PATIENT_CHAR, OUTCOME, DOSAGE
    - Add EntityRuler to SpaCy pipeline with custom patterns
    - Implement `extract_entities(text: str) -> List[Entity]` method
    - Implement `get_sentence_context(entity_span, doc) -> str` helper method
    - _Requirements: 2.1, 2.3, 2.4, 2.5, 2.6, 2.7, 2.8, 2.9_

  - [ ]* 3.2 Write unit tests for entity extraction
    - Test extraction of DRUG entities with example narrative containing "aspirin"
    - Test extraction of ADVERSE_EVENT entities with example narrative containing "gastric bleeding"
    - Test extraction of INDICATION entities with example narrative containing "hypertension"
    - Test extraction of PATIENT_CHAR entities with example narrative containing "65-year-old male"
    - Test extraction of OUTCOME entities with example narrative containing "recovered"
    - Test extraction of DOSAGE entities with example narrative containing "100mg daily"
    - _Requirements: 2.3, 2.4, 2.5, 2.6, 2.7, 2.8_

  - [ ]* 3.3 Write property test for entity context preservation
    - **Property 3: Entity Context Preservation**
    - **Validates: Requirements 2.9**

- [ ] 4. Implement entity normalization component
  - [ ] 4.1 Create EntityNormalizer class
    - Implement `normalize_entity(entity: Entity) -> Entity` method
    - Implement `normalize_case(text: str) -> str` helper (lowercase with acronym handling)
    - Implement `remove_extra_whitespace(text: str) -> str` helper
    - Handle normalization failures by retaining original text
    - _Requirements: 3.1, 3.2, 3.3_

  - [ ]* 4.2 Write property test for entity normalization completeness
    - **Property 4: Entity Normalization Completeness**
    - **Validates: Requirements 3.1, 3.2**

  - [ ]* 4.3 Write property test for case-insensitive normalization
    - **Property 5: Case-Insensitive Normalization**
    - **Validates: Requirements 3.3**

- [ ] 5. Implement relation extraction component
  - [ ] 5.1 Create RelationExtractor class with SpaCy dependency parsing
    - Initialize with SpaCy pipeline
    - Implement `extract_relations(text: str, entities: List[Entity]) -> List[Relation]` method
    - Implement `identify_causation(token1, token2, doc) -> Optional[Relation]` using dependency patterns
    - Implement `identify_treatment(token1, token2, doc) -> Optional[Relation]` using dependency patterns
    - Implement `identify_experience(token1, token2, doc) -> Optional[Relation]` using dependency patterns
    - Implement `identify_association(token1, token2, doc) -> Optional[Relation]` using proximity/conjunction patterns
    - Use SpaCy Matcher for pattern-based relation detection
    - _Requirements: 4.1, 4.3, 4.4, 4.5, 4.6, 4.7, 4.8_

  - [ ]* 5.2 Write unit tests for relation extraction
    - Test CAUSES relationship with example: "Ibuprofen caused severe gastric bleeding"
    - Test TREATS relationship with example: "Aspirin treats cardiovascular disease"
    - Test EXPERIENCED_BY relationship with example: "Patient experienced nausea"
    - Test ASSOCIATED_WITH relationship with example: "Headache and dizziness occurred together"
    - _Requirements: 4.4, 4.5, 4.6, 4.7_

  - [ ]* 5.3 Write property test for relationship source and target capture
    - **Property 6: Relationship Source and Target Capture**
    - **Validates: Requirements 4.8**

- [ ] 6. Checkpoint - Ensure NLP components work end-to-end
  - Ensure all tests pass, ask the user if questions arise.

- [ ] 7. Implement structured output generation
  - [ ] 7.1 Create StructuredOutputGenerator class
    - Implement `create_structured_output(entities: List[Entity], relations: List[Relation]) -> pd.DataFrame` method
    - Implement `format_entity_row(entity: Entity) -> Dict` helper
    - Implement `format_relation_row(relation: Relation) -> Dict` helper
    - Ensure DataFrame has columns: Entity Type, Entity Value, Relation Type, Source Entity, Target Entity, Sentence Context
    - Handle empty entity/relation lists by returning DataFrame with headers only
    - _Requirements: 5.1, 5.2, 5.3, 5.4, 5.5, 5.6, 5.7, 5.8_

  - [ ]* 7.2 Write property test for structured output format
    - **Property 7: Structured Output Format**
    - **Validates: Requirements 5.1, 5.2, 5.3, 5.4, 5.5, 5.6, 5.7**

  - [ ]* 7.3 Write unit test for empty input handling
    - Test that empty entity/relation lists produce DataFrame with correct headers
    - _Requirements: 5.8_

- [ ] 8. Implement Neo4j graph store integration
  - [ ] 8.1 Create Neo4jGraphStore class
    - Implement `__init__(uri: str, username: str, password: str)` with connection setup
    - Implement `create_schema()` method with Cypher queries for constraints and indexes
    - Define node types: Drug, AdverseEvent, Patient, Indication
    - Define relationship types: CAUSES, TREATS, EXPERIENCED_BY, ASSOCIATED_WITH
    - Implement `close()` method for connection cleanup
    - _Requirements: 6.1, 6.2, 6.3, 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.12_

  - [ ] 8.2 Implement entity and relation ingestion methods
    - Implement `ingest_entity(entity: Entity) -> bool` with MERGE Cypher query
    - Implement `ingest_relation(relation: Relation) -> bool` with MERGE Cypher query
    - Implement retry logic with exponential backoff for transient failures
    - Add error handling and logging for all database operations
    - _Requirements: 6.10, 6.11, 7.5, 10.2, 10.4_

  - [ ]* 8.3 Write unit tests for Neo4j schema creation
    - Test that schema creation defines Drug, AdverseEvent, Patient, Indication node types
    - Test that schema creation defines CAUSES, TREATS, EXPERIENCED_BY, ASSOCIATED_WITH relationship types
    - _Requirements: 6.2, 6.3, 6.4, 6.5, 6.6, 6.7, 6.8, 6.9_

  - [ ]* 8.4 Write property test for entity-to-node round trip
    - **Property 9: Entity-to-Node Round Trip**
    - **Validates: Requirements 6.10**

  - [ ]* 8.5 Write property test for relationship-to-edge round trip
    - **Property 10: Relationship-to-Edge Round Trip**
    - **Validates: Requirements 6.11**

- [ ] 9. Implement error handling and logging
  - [ ] 9.1 Create error handling utilities
    - Implement `validate_input(text: str) -> Tuple[bool, Optional[str]]` for input validation
    - Implement `extract_entities_safe(text: str) -> Tuple[List[Entity], Optional[str]]` with try-catch
    - Implement `ingest_with_retry(entity: Entity, max_retries: int) -> bool` with retry logic
    - Configure logging with appropriate levels (DEBUG, INFO, WARNING, ERROR, CRITICAL)
    - Create user-friendly error message mappings
    - _Requirements: 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 10.2, 10.3, 10.5_

  - [ ]* 9.2 Write property test for error logging completeness
    - **Property 11: Error Logging Completeness**
    - **Validates: Requirements 7.1, 7.5**

  - [ ]* 9.3 Write property test for error message display
    - **Property 12: Error Message Display**
    - **Validates: Requirements 7.2**

  - [ ]* 9.4 Write property test for system stability after errors
    - **Property 13: System Stability After Errors**
    - **Validates: Requirements 7.6**

  - [ ]* 9.5 Write unit tests for error handling edge cases
    - Test empty input validation
    - Test text exceeding maximum length
    - Test Neo4j connection failure handling
    - Test no entities found scenario
    - _Requirements: 7.3, 7.4_

- [ ] 10. Implement NLP processor orchestration
  - [ ] 10.1 Create NLPProcessor class to orchestrate components
    - Implement `__init__()` to initialize EntityExtractor, EntityNormalizer, RelationExtractor
    - Implement `process_narrative(text: str) -> Tuple[List[Entity], List[Relation], Optional[str]]` method
    - Integrate entity extraction, normalization, and relation extraction
    - Add input validation before processing
    - Add comprehensive logging for all processing steps
    - _Requirements: 1.2, 8.1, 8.2, 8.3, 10.3, 10.5_

  - [ ]* 10.2 Write property test for input validation before processing
    - **Property 14: Input Validation Before Processing**
    - **Validates: Requirements 10.3**

  - [ ]* 10.3 Write property test for processing activity logging
    - **Property 15: Processing Activity Logging**
    - **Validates: Requirements 10.5**

- [ ] 11. Checkpoint - Ensure all backend components integrated
  - Ensure all tests pass, ask the user if questions arise.

- [ ] 12. Implement Streamlit web interface
  - [ ] 12.1 Create Streamlit app with input and display components
    - Create `app.py` as main Streamlit application
    - Implement `render_input_form()` with text area for PV narrative input
    - Implement `display_results(df: pd.DataFrame)` to render DataFrame as table
    - Implement `show_error(message: str)` to display error messages
    - Implement `show_processing_status()` with spinner during processing
    - Add input validation with error message display for empty input
    - Integrate with NLPProcessor for text processing
    - Integrate with StructuredOutputGenerator for result display
    - _Requirements: 1.1, 1.2, 1.3, 1.4, 1.5, 5.9_

  - [ ] 12.2 Add Neo4j integration toggle and status display
    - Add checkbox to enable/disable Neo4j ingestion
    - Display connection status and ingestion results
    - Handle Neo4j connection failures gracefully with error messages
    - _Requirements: 6.10, 6.11, 7.4_

  - [ ]* 12.3 Write property test for text input acceptance
    - **Property 1: Text Input Acceptance**
    - **Validates: Requirements 1.1, 1.2**

  - [ ]* 12.4 Write property test for multi-line text support
    - **Property 2: Multi-line Text Support**
    - **Validates: Requirements 1.3**

  - [ ]* 12.5 Write property test for output display integration
    - **Property 8: Output Display Integration**
    - **Validates: Requirements 5.9**

  - [ ]* 12.6 Write unit test for empty input edge case
    - Test that empty input displays error message and prevents submission
    - _Requirements: 1.4_

- [ ] 13. Create example data and documentation
  - [ ] 13.1 Create example PV narratives and expected outputs
    - Create `examples/sample_narratives.txt` with 5-10 example PV reports
    - Create `examples/expected_outputs.csv` with expected entity-relation tables
    - Document entity patterns in `docs/entity_patterns.md`
    - Document relation patterns in `docs/relation_patterns.md`
    - _Requirements: 9.5_

  - [ ] 13.2 Create comprehensive documentation
    - Update README.md with setup instructions, usage examples, and architecture overview
    - Create `docs/assumptions_and_limitations.md` documenting system constraints
    - Add docstrings to all public functions and classes
    - Add inline comments for complex NLP logic
    - Create `docs/neo4j_schema.md` with graph schema and Cypher queries
    - _Requirements: 9.1, 9.2, 9.3, 9.4_

- [ ] 14. Integration testing and final validation
  - [ ]* 14.1 Write end-to-end integration tests
    - Test complete flow: input → entity extraction → relation extraction → structured output
    - Test complete flow with Neo4j: input → processing → graph ingestion → query validation
    - Test error recovery scenarios
    - _Requirements: 1.1, 1.2, 2.3, 4.4, 5.1, 6.10, 6.11_

  - [ ] 14.2 Create test fixtures and sample data
    - Create test Neo4j instance configuration
    - Create fixture PV narratives with known entities and relations
    - Create mock objects for testing without external dependencies
    - _Requirements: 9.5_

- [ ] 15. Final checkpoint - Complete system validation
  - Run all unit tests and property tests
  - Verify all 15 correctness properties pass
  - Test with example PV narratives
  - Verify Neo4j integration works end-to-end
  - Ensure all documentation is complete
  - Ask the user if questions arise or if ready for deployment

## Notes

- Tasks marked with `*` are optional test tasks that can be skipped for faster MVP
- Each task references specific requirements for traceability
- Property tests validate universal correctness properties (minimum 100 iterations each)
- Unit tests validate specific examples and edge cases
- Checkpoints ensure incremental validation at major milestones
- SpaCy model (`en_core_web_sm`) must be downloaded: `python -m spacy download en_core_web_sm`
- Neo4j AuraDB credentials must be configured in `.env` file before testing graph integration
- All property tests should be tagged with: `# Feature: pv-nlp-knowledge-graph, Property {N}: {property_text}`
