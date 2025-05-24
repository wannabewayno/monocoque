# Monocoque Template
Opinionated framework using provider-consumer architecture for documentation driven projects.
Build event-driven applications with strict versioning, automated SDK generation, and infrastructure-as-code.

Designed for teams that value:  
✅ **Explicit contracts** between services  
✅ **Zero manual documentation** (generate from runtime)  
✅ **Independent versioning** with cascading updates  

## 🏗️ Structure

```bash
.
├── Apps/                # Deployable applications
│   └── [app-name]/      # Application code
├── Packages/            # Reusable libraries (versioned)
│   └── [package-name]/  # Pacakge code
├── Infra/               # IaC (AWS CDK/Terraform)
│   ├── [stack-name].ts  # Application deployment config
│   └── main.ts          # Infra entrypoint (deploys all stacks)
└── .changeset/          # Versioning metadata
```

## ✨ Why Use This?

| Feature                     | Benefit                                                          |
|-----------------------------|------------------------------------------------------------------|
| **Provider-Consumer Model** | Apps communicate via versioned SDKs.                             |
| **Docs → SDK Automation**   | OpenAPI/AsyncAPI specs auto-generate clients.                    |
| **SemVer Enforcement**      | Breaking changes are detected from docs, not code.               |
| **Monorepo Isolation**      | Apps/packages are versioned independently but share tooling.     |

## 🚀 Getting Started

### 1. Add a New App
```bash
./scripts/add-app.sh --name payment-service --language go --consumes auth --provides http
```
This will:
- Scaffold a Go app with DI (Wire).
- Install the `auth-service` SDK if it exists.
- Configure OpenAPI doc generation for HTTP endpoints.

### 2. Make Changes
```bash
git commit -m "feat(payment-service): add refund endpoint"
```
- 🔍 **Pre-commit hooks** enforce:  
  - Code formatting (`gofmt`, `biome`).  
  - Scope-bound commits (can’t modify `auth-service` from a `payment-service` commit).  

### 3. Release
```bash
npx changeset add    # Select packages to version
npx changeset version
git push --follow-tags
```
- CI will:  
  - Generate SDKs from updated docs.  
  - Deploy apps via `Infra/stacks/`.  

## 🔧 Key Tools

| Purpose               | Tool(s)                              |
|-----------------------|--------------------------------------|
| Versioning            | `Changesets` + SemVer               |
| SDK Generation        | `oapi-codegen` (Go), `openapi-typescript` (TS) |
| Dependency Injection  | `Wire` (Go), `Inversify` (TS)       |
| IaC                   | AWS CDK (TypeScript)                |
| Linting/Formatting    | `biome` (TS/JS), `gofmt` (Go)       |

## 📜 Rules of the Road

1. **Docs Are Truth**  
- APIs are defined by OpenAPI/AsyncAPI specs (generated from runtime).  
- SDKs are **always** generated, never handwritten.  

1. **Break Glass for Breaking Changes**  
- `major` version bumps require:  
  - Migration guide in `docs/`.  
  - Approval via Changeset.  

1. **Infra Follows Apps**  
- Each app’s infrastructure lives in `Infra/[app-name].ts`.  

---

## ▶️ **Next Steps**
1. Explore `/Apps/examples` for reference implementations.  
2. Run `./scripts/add-app.sh --help` to scaffold your first service.  
3. Edit `Infra/stacks` to match your cloud provider.  

**Tip:** Use `deps.json` to visualize app dependencies:  
```bash
cat deps.json | jq .
```