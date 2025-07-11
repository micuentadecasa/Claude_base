# Spanish NES Document Analysis System

## Overview
An intelligent document analysis system that validates documents against Spanish NES (Esquema Nacional de Seguridad) compliance standards.

## Core Requirements

### Document Processing
- Support PDF and DOCX formats
- Extract text content reliably
- Handle corrupted or malformed files gracefully
- Process documents up to 10MB efficiently

### NES Compliance Validation
- **Backups**: Validate backup procedures, frequency, verification, remote storage
- **Access Control**: Check authentication systems, MFA, privilege management, audit logs
- **Monitoring**: Verify network monitoring, intrusion detection, incident response procedures

### User Interface
- Chat-based interaction in Spanish
- Progressive document analysis workflow
- Clear validation results with specific NES recommendations
- Support for multiple document uploads

### Technical Requirements
- Use Google Gemini via LiteLLM for analysis
- Implement LangWatch monitoring for all LLM calls
- Follow SOLID architecture principles
- Comprehensive error handling and logging
- 90%+ test coverage with realistic scenarios

## Success Criteria
- Accurately identifies NES compliance gaps
- Processes documents within 30 seconds
- Provides actionable recommendations
- Handles edge cases gracefully
- Maintains conversation context across interactions