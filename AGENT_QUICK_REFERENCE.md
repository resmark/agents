# RESMARK Agent v2.0 - Quick Reference Card

## 🎯 Primary Mission
Safe migration of RESMARK platform from Angular/Node.js to Next.js/React with proto-microservices architecture using NX libraries and anti-corruption patterns.

## ✅ Status Check Commands
```bash
# Services Health
curl http://localhost:3001/health    # Backend health
curl http://localhost:3010            # Frontend check

# Validation Suite
npm run validate:ai                   # Full anti-pattern check
npm run type-check                    # TypeScript validation
npm test                             # Run all tests

# NX Operations
npx nx graph                          # View dependencies
npx nx build search-service           # Build specific library
npx nx affected:test                  # Test affected only
```

## 🚫 Critical Anti-Patterns to Prevent

### Legacy Session Patterns
```typescript
// ✅ ONLY THESE EXIST
req.session.entityId              // Source of truth
req.session.userAccessMap         // Permissions
req.session.businessEntityList    // Available entities

// ❌ AI HALLUCINATIONS - REJECT THESE
req.user.businessEntities         // Does not exist!
req.currentEntity                 // Does not exist!
req.session.permissions           // Does not exist!
req.user.entityId                // Does not exist!
```

### Entity Scoping (MANDATORY)
```typescript
// ✅ ALWAYS include entityId
await collection.find({
  entityId: request.entityId,     // REQUIRED
  ...otherCriteria
});

// ❌ NEVER query without entity
await collection.find({            // SECURITY VIOLATION
  // Missing entityId
});
```

### No Mock Data in Services
```typescript
// ❌ WRONG - Mock data
async getUsers() {
  return [{ id: 1, name: 'Mock' }];
}

// ✅ CORRECT - Real database
async getUsers() {
  return await this.repo.findByEntity(entityId);
}
```

## 📋 Task Management Pattern

### Use TodoWrite for ALL multi-step operations:
1. Break down immediately upon receiving complex task
2. Update status in real-time (not batch)
3. Mark completed immediately after each step
4. Add discovered tasks as pending

### Status Communication
```
✅ Completed: [What was done]
🔄 In Progress: [Current action]
⚠️ Issue Found: [Problem description]
✅ Fixed: [Resolution]
```

## 🔒 Git Safety Rules

### Before ANY git operation:
1. **Explain impact:** "This will only affect staging, not your changes"
2. **Show what's affected:** `git status --short`
3. **Be selective:** Stage only relevant files
4. **Confirm destructive ops:** Never use `--force` or `--hard` without approval

### Safe Commit Pattern:
```bash
git status --short | grep -E "^M "   # Show modified
git add [specific-paths]              # Selective staging
git diff --cached --stat             # Review staged
git commit -m "feat: [description]"  # Clear message
```

## 🏗️ Service Development Checklist

When creating new services:
- [ ] Define contracts in `libs/shared/contracts/`
- [ ] Create NX library with `npx nx g @nx/node:library`
- [ ] Implement with entity scoping
- [ ] Add anti-corruption layer if touching legacy
- [ ] Configure TypeScript paths in `tsconfig.base.json`
- [ ] Create Express routes with validation
- [ ] Add comprehensive tests
- [ ] Document API and architecture
- [ ] Run `npm run validate:ai`
- [ ] Check service health endpoint

## 🔍 Debugging Workflows

### TypeScript Resolution Issues
```bash
ls libs/services/[service]           # Verify exists
grep "@resmark/[service]" tsconfig.base.json  # Check paths
npx nx reset                         # Clear cache
npx nx build [service]               # Rebuild
# Restart TS server in IDE
```

### Service Connection Failures
```bash
curl http://localhost:[port]/health  # Check service
cat .env | grep -E "MONGODB|REDIS"  # Verify env
make docker-logs                    # Check containers
make mongo-shell                    # Test DB connection
make docker-restart                 # Restart if needed
```

## 📊 Performance Monitoring

### Check Background Processes
```bash
# Check for errors periodically
BashOutput --bash_id [id] --filter "error|Error|ERROR"

# Summarize status after major operations
"✅ Backend (3001) and Frontend (3010) running successfully"
```

### Service Health Pattern
```typescript
async getHealth() {
  try {
    await this.db.admin().ping();
    return {
      status: 'healthy',
      service: 'my-service',
      timestamp: new Date(),
      details: { database: 'connected' }
    };
  } catch (error) {
    return { status: 'unavailable', error };
  }
}
```

## 🚀 Quick Wins

### Validate Before Committing
```bash
npm run validate:ai              # Check anti-patterns
npm run type-check              # TypeScript check
npm test                        # Run tests
npm run lint                    # Code style
```

### Monitor Services
```bash
curl http://localhost:3001/health | jq .  # Backend health
curl http://localhost:3010               # Frontend check
make health                              # All services
```

### NX Power Commands
```bash
npx nx affected:test            # Test only changes
npx nx affected:build          # Build only changes
npx nx graph                   # Visual dependencies
npx nx reset                   # Clear all caches
```

## 🎭 Agent Improvements (v2.0)

**Key Changes from v1.0:**
1. **Safety First:** Always explain potentially destructive operations
2. **Selective Actions:** Don't stage everything automatically
3. **Active Monitoring:** Check background processes periodically
4. **Proactive Planning:** Use TodoWrite extensively
5. **Pattern Enforcement:** Strict anti-pattern detection
6. **Clear Communication:** Status updates with emojis
7. **Systematic Debugging:** Step-by-step workflows
8. **Documentation Quality:** Hierarchical and visual
9. **Testing Rigor:** Always validate before claiming done
10. **Migration Safety:** Feature flags and gradual rollouts

---
*Quick Reference v2.0 - Based on October 1, 2025 session analysis*
*Focus: Safety, clarity, and systematic validation in RESMARK migration*