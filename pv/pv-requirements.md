# Requirements Document

## Introduction

This document specifies the requirements for a Pharmacovigilance (PV) NLP application that processes free-text adverse event reports using Natural Language Processing to extract entities and relationships, then stores them in a Neo4j knowledge graph. The system enables automated extraction of pharmacovigilance-relevant information from unstructured clinical narratives to support drug safety monitoring and analysis.

## Glossary

- **PV_System**: The Pharmacovigilance NLP Knowledge Graph application
- **Input_Interface**: The web-based or Streamlit user interface component
- **NLP_Processor**: The SpaCy-based natural language processing component
- **Entity_Extractor**: The component responsible for Named Entity Recognition
- **Relation_Extractor**: The component responsible for identifying relationships between entities
- **Knowledge_Graph**: The Neo4j AuraDB graph database storing entities and relationships
- **PV_Narrative**: Free-text adverse event report or case description
- **Entity**: A recognized pharmacovigilance-relevant concept (Drug, Adverse Event, Indication, Patient Characteristic, Outcome, Dosage)
- **Relationship**: A semantic connection between two entities
- **Structured_Output**: Tabular representation of extracted entities and relationships

## Requirements

### Requirement 1: Accept Pharmacovigilance Narratives

**User Story:** As a pharmacovigilance analyst, I want to input free-text adverse event reports, so that I can extract structured information from unstructured clinical narratives.

#### Acceptance Criteria

1. THE Input_Interface SHALL accept free-text pharmacovigilance narratives as input
2. WHEN a user submits a PV_Narrative, THE Input_Interface SHALL pass the text to the NLP_Processor
3. THE Input_Interface SHALL support narratives containing multiple sentences and paragraphs
4. WHEN the input field is empty, THE Input_Interface SHALL prevent submission and display an error message
5. THE Input_Interface SHALL provide clear visual feedback during processing

### Requirement 2: Extract Named Entities Using SpaCy

**User Story:** As a pharmacovigilance analyst, I want the system to identify relevant entities in adverse event reports, so that I can understand what drugs, events, and patient characteristics are mentioned.

#### Acceptance Criteria

1. THE Entity_Extractor SHALL use SpaCy for Named Entity Recognition
2. THE Entity_Extractor SHALL NOT use HuggingFace models or external LLMs
3. WHEN processing a PV_Narrative, THE Entity_Extractor SHALL identify entities of type Drug
4. WHEN processing a PV_Narrative, THE Entity_Extractor SHALL identify entities of type Adverse Event
5. WHEN processing a PV_Narrative, THE Entity_Extractor SHALL identify entities of type Indication
6. WHEN processing a PV_Narrative, THE Entity_Extractor SHALL identify entities of type Patient Characteristic
7. WHEN processing a PV_Narrative, THE Entity_Extractor SHALL identify entities of type Outcome
8. WHEN processing a PV_Narrative, THE Entity_Extractor SHALL identify entities of type Dosage
9. WHEN an entity is extracted, THE Entity_Extractor SHALL capture the sentence context containing that entity

### Requirement 3: Normalize Extracted Entities

**User Story:** As a pharmacovigilance analyst, I want entity values to be normalized, so that variations of the same concept are represented consistently.

#### Acceptance Criteria

1. WHEN an entity is extracted, THE Entity_Extractor SHALL attempt to normalize the entity value
2. WHEN normalization fails, THE Entity_Extractor SHALL retain the original extracted text
3. THE Entity_Extractor SHALL handle case variations in entity text

### Requirement 4: Extract Relationships Between Entities

**User Story:** As a pharmacovigilance analyst, I want to understand how entities relate to each other, so that I can identify causal relationships and associations in adverse event reports.

#### Acceptance Criteria

1. THE Relation_Extractor SHALL use SpaCy for relationship extraction
2. THE Relation_Extractor SHALL NOT use rule engines outside SpaCy
3. WHEN entities are present in a sentence, THE Relation_Extractor SHALL identify relationships between them
4. THE Relation_Extractor SHALL identify CAUSES relationships between Drug and Adverse Event entities
5. THE Relation_Extractor SHALL identify TREATS relationships between Drug and Indication entities
6. THE Relation_Extractor SHALL identify EXPERIENCED_BY relationships between Adverse Event and Patient entities
7. THE Relation_Extractor SHALL identify ASSOCIATED_WITH relationships between related entities
8. WHEN a relationship is identified, THE Relation_Extractor SHALL capture the source entity and target entity

### Requirement 5: Generate Structured Tabular Output

**User Story:** As a pharmacovigilance analyst, I want to see extracted information in a structured table, so that I can review and validate the extraction results.

#### Acceptance Criteria

1. THE PV_System SHALL convert extracted entities and relationships into tabular format
2. THE Structured_Output SHALL include an Entity Type column
3. THE Structured_Output SHALL include an Entity Value column
4. THE Structured_Output SHALL include a Relation Type column
5. THE Structured_Output SHALL include a Source Entity column
6. THE Structured_Output SHALL include a Target Entity column
7. THE Structured_Output SHALL include a Sentence Context column
8. WHEN no entities are extracted, THE PV_System SHALL return an empty table with column headers
9. THE PV_System SHALL display the Structured_Output to the user through the Input_Interface

### Requirement 6: Store Data in Neo4j Knowledge Graph

**User Story:** As a pharmacovigilance analyst, I want extracted information stored in a knowledge graph, so that I can perform complex queries and analyze patterns across multiple reports.

#### Acceptance Criteria

1. THE Knowledge_Graph SHALL use Neo4j AuraDB as the graph database
2. THE Knowledge_Graph SHALL define a Drug node type
3. THE Knowledge_Graph SHALL define an AdverseEvent node type
4. THE Knowledge_Graph SHALL define a Patient node type
5. THE Knowledge_Graph SHALL define an Indication node type
6. THE Knowledge_Graph SHALL define a CAUSES relationship type
7. THE Knowledge_Graph SHALL define a TREATS relationship type
8. THE Knowledge_Graph SHALL define an EXPERIENCED_BY relationship type
9. THE Knowledge_Graph SHALL define an ASSOCIATED_WITH relationship type
10. WHEN entities are extracted, THE PV_System SHALL create corresponding nodes in the Knowledge_Graph
11. WHEN relationships are extracted, THE PV_System SHALL create corresponding edges in the Knowledge_Graph
12. THE PV_System SHALL use Cypher queries for data ingestion into Neo4j

### Requirement 7: Handle Processing Errors Gracefully

**User Story:** As a pharmacovigilance analyst, I want the system to handle errors gracefully, so that processing failures don't crash the application and I receive informative error messages.

#### Acceptance Criteria

1. WHEN the NLP_Processor encounters an error, THE PV_System SHALL log the error details
2. WHEN the NLP_Processor encounters an error, THE PV_System SHALL display a user-friendly error message
3. WHEN no entities are found in a PV_Narrative, THE PV_System SHALL inform the user that no entities were detected
4. WHEN the Knowledge_Graph connection fails, THE PV_System SHALL display a connection error message
5. WHEN a Cypher query fails, THE PV_System SHALL log the query and error details
6. IF an error occurs during processing, THEN THE PV_System SHALL maintain system stability and allow subsequent processing attempts

### Requirement 8: Maintain Modular Architecture

**User Story:** As a developer, I want the system to have a modular architecture, so that components can be maintained, tested, and extended independently.

#### Acceptance Criteria

1. THE PV_System SHALL separate the Input_Interface from the NLP_Processor
2. THE PV_System SHALL separate the Entity_Extractor from the Relation_Extractor
3. THE PV_System SHALL separate the NLP_Processor from the Knowledge_Graph integration
4. WHEN the Input_Interface implementation changes, THE NLP_Processor SHALL continue functioning without modification
5. WHEN the Knowledge_Graph schema changes, THE NLP_Processor SHALL continue functioning without modification
6. THE PV_System SHALL use well-defined interfaces between components

### Requirement 9: Provide Code Documentation

**User Story:** As a developer, I want well-documented code, so that I can understand, maintain, and extend the system.

#### Acceptance Criteria

1. THE PV_System SHALL include docstrings for all public functions and classes
2. THE PV_System SHALL include inline comments explaining complex logic
3. THE PV_System SHALL include a README file with setup instructions
4. THE PV_System SHALL document assumptions and limitations
5. THE PV_System SHALL include example usage for key components

### Requirement 10: Support Production Deployment

**User Story:** As a system administrator, I want the application to be production-ready, so that it can be deployed reliably in a clinical environment.

#### Acceptance Criteria

1. THE PV_System SHALL use Python as the programming language
2. THE PV_System SHALL include error handling for all external service calls
3. THE PV_System SHALL validate input data before processing
4. THE PV_System SHALL use connection pooling for database connections
5. THE PV_System SHALL log all processing activities with appropriate log levels
6. THE PV_System SHALL include configuration management for environment-specific settings
