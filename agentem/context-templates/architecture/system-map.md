# System Architecture Map

## Services

### [Service Name]
- **Owner:** [Team/Person]
- **Language/Stack:** [e.g., Python/FastAPI, TypeScript/Next.js]
- **Repository:** [Repo URL or path]
- **Purpose:** [One sentence]
- **Key Dependencies:** [What it talks to]
- **Database:** [DB type + name]
- **Deployment:** [Where it runs]

### [Next Service]
<!-- Add one block per service. The agents use this to understand blast radius, route reviews to the right people, and flag cross-service dependencies in specs. -->

## Data Flow

1. **User Request Flow:** Client → [API Gateway] → [Service A] → [Service B] → Database
2. **Background Processing:** Queue → [Worker Service] → [Storage] → [Notification Service]
3. **Analytics Pipeline:** Events → [Collector] → [Data Warehouse] → [Dashboard]

## Shared Infrastructure
- **Auth:** [How authentication works, what service handles it]
- **Messaging:** [Queue system, pub/sub]
- **Storage:** [Object storage, CDN]
- **Monitoring:** [What tools, where dashboards live]

## Known Architectural Constraints
<!-- Non-negotiable rules. Agents will flag specs that violate these. -->
- [Constraint 1: e.g., "All new services must use the shared auth library"]
- [Constraint 2: e.g., "Database migrations require a 2-week lead time for DBA review"]
