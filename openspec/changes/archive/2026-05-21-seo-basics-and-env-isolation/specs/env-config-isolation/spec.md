## ADDED Requirements

### Requirement: JWT secret externalized to environment variable
The backend SHALL read the JWT signing secret from the `JWT_SECRET` environment variable rather than from a hardcoded value in source-controlled configuration files.

#### Scenario: JWT secret loaded from environment
- **WHEN** the application starts with JWT_SECRET environment variable set
- **THEN** JWT tokens SHALL be signed using the provided secret

#### Scenario: Dev fallback secret used when JWT_SECRET not set
- **WHEN** the application starts locally without JWT_SECRET set
- **THEN** a clearly-labeled dev-only default secret SHALL be used, and the application SHALL start successfully

### Requirement: CORS origins externalized to configuration
Both SecurityConfig and WebConfig SHALL read allowed CORS origins from the `app.cors.allowed-origins` configuration property.

#### Scenario: CORS origins configurable without code change
- **WHEN** `app.cors.allowed-origins` is set to a production domain (e.g., https://app.example.com)
- **THEN** the API SHALL accept cross-origin requests from that domain

#### Scenario: Default CORS origin preserved for local development
- **WHEN** `app.cors.allowed-origins` is not explicitly set
- **THEN** CORS SHALL default to allowing http://localhost:5173

#### Scenario: Multiple CORS origins supported
- **WHEN** `app.cors.allowed-origins` contains a comma-separated list of origins
- **THEN** all listed origins SHALL be permitted by CORS policy

### Requirement: Production environment configuration template
A `application-prod.yaml` file SHALL exist in the backend resources, documenting all required production environment variables as `${ENV_VAR}` placeholders.

#### Scenario: Production profile activates prod config
- **WHEN** the application starts with spring.profiles.active=prod
- **THEN** `application-prod.yaml` SHALL override base configuration for datasource, mail, and CORS settings

#### Scenario: Application fails fast if required prod vars missing
- **WHEN** the application starts in production profile without required env vars (e.g., DB_URL, JWT_SECRET)
- **THEN** Spring Boot SHALL fail to start with a clear error indicating which properties are missing
