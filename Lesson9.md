# Lesson 9 : Vulnerable Dependencies
## Vulnerability overview
The vulnerable implementation uses node-serialize to deserialize untrusted input from API Gateway requests:
- serialize.unserialize(event.body)
- serialize.unserialize(event.headers)
This allows attacker-controlled input to be converted into executable JavaScript objects. Since the library supports function reconstruction, it can lead to Remote Code Execution (RCE) if malicious payloads are provided.
The issue is worsened by the fact that Lambda processes external API requests, making the deserialization step a direct attack entry point.
## Impact
If exploited, this vulnerability can allow an attacker to:
- Execute arbitrary JavaScript code inside the Lambda environment
- Manipulate backend logic and trigger internal Lambda functions
## Fix Implementation
1. Removed the node-serialize dependency from package.json to eliminate unsafe deserialization capabilities.
2. Replaced unsafe deserialization (serialize.unserialize) with JSON.parse() in order-manager.js to ensure input is treated as structured data only.
3. Added error handling using try/catch to safely manage malformed JSON input and prevent runtime crashes.
4. Implemented input validation and defensive checks for undefined or missing fields before processing requests.
5. Ensured that all user-controlled input is treated strictly as data and never interpreted or executed as code, preventing Remote Code Execution (RCE).
   
