# Spanish NES Document Analysis System

## Overview
An intelligent chatbot system that has a initial list of questions for the ENS spanish security schema, and ask these questions to the user, with the answer from the user, it validates that it has all the infomration that needs to fill this question in the ENS, if it hasnt all the info it should ask more questions to the user to complete the ENS answer, once it has all the data neeeded for the ENS for this question, then store it. all the information is stored in simple jsons. remember that we are talking about the Spanish NES (Esquema Nacional de Seguridad) compliance standards.
the user can ask the chat to remove some answer, get infomration about answers, statistics about questions answered, level of perfection of the questions, etc

## Core Requirements



### NES Compliance Validation
- **Backups**: Validate backup procedures, frequency, verification, remote storage
- **Access Control**: Check authentication systems, MFA, privilege management, audit logs
- **Monitoring**: Verify network monitoring, intrusion detection, incident response procedures

### User Interface
- Chat-based interaction in Spanish
- Clear validation results with specific NES recommendations

### Technical Requirements
- Use Google Gemini via LiteLLM for analysis
- Implement LangWatch studio monitoring for all LLM calls
- Follow SOLID architecture principles
- Comprehensive error handling and logging
- 90%+ test coverage with realistic scenarios

## Success Criteria
- Accurately identifies NES compliance gaps
- Provides actionable recommendations
- Handles edge cases gracefully
- Maintains conversation context across interactions