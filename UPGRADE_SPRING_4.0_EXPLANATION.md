# Spring Boot Upgrade: 3.5.8 → 4.0.0

## Executive Summary

**Status:** ⚠️ BLOCKED - Requires Manual Migration
**Upgrade Type:** MAJOR Version Upgrade
**From Version:** 3.5.8
**Target Version:** 4.0.0
**Date:** 2025-12-04
**Related PR:** [#11](https://github.com/rachael-dot/spring-petclinic-rest/pull/11) (3.5.8 upgrade)

⚠️ **WARNING: This is a MAJOR version upgrade with architectural breaking changes**

**CONCLUSION:** Spring Boot 4.0.0 introduces fundamental modular architecture changes that prevent automated migration. Manual migration required following Spring's official guide.

## Upgrade Rationale

This is a major upgrade from Spring Boot 3.5.8 to 4.0.0. Major upgrades include:
- Breaking API changes
- Major dependency upgrades
- Configuration changes
- Potential code modifications required
- Comprehensive testing required

**Why Spring Boot 4.0?**
- Spring Framework 7.0 with latest features
- Spring Security 7.0 improvements
- Jakarta EE 3.0+ compliance
- Jackson 3.0 support
- Hibernate 7.1 features
- Java 25 support (while maintaining Java 17 compatibility)
- Modularized codebase for smaller artifacts

## Version Compatibility Analysis

### Java Version Compatibility
- **Current Spring Boot Version:** 3.5.8 (requires Java 17+)
- **Target Spring Boot Version:** 4.0.0 (requires Java 17+, supports Java 25)
- **Current Java Version:** Java 25
- **Compatibility:** ✅ No Java version upgrade required

### Upgrade Type Assessment
- **Major Upgrade (3.5.8 → 4.0.0)**
  - HIGH risk
  - Breaking changes expected
  - Major dependency version jumps
  - Extensive testing required
  - OpenRewrite migration recommended

## Pre-Upgrade State

### Current Dependencies
- **Spring Boot Parent:** 3.5.8
- **Spring Framework:** 6.2.14
- **Hibernate:** 6.6.36.Final
- **Spring Data JDBC:** 1.2.1.RELEASE
- **Springdoc OpenAPI:** 2.8.13
- **MapStruct:** 1.6.3
- **Jackson Databind Nullable:** 0.2.8

### Pre-Upgrade Build and Test Results
✅ **All tests passed on Spring Boot 3.5.8**

**Test Summary:**
- Total: 73 tests passed
- Build: SUCCESS
- Configuration: Stable

---

## Spring Boot 4.0.0 Breaking Changes Analysis

### Major Dependency Upgrades

| Component | From | To | Impact |
|-----------|------|-----|--------|
| Spring Framework | 6.2.14 | 7.0 | HIGH - API changes expected |
| Spring Security | 6.x | 7.0 | HIGH - Security config changes |
| Spring Data | 2024.x | 2025.1 | MEDIUM - Repository changes possible |
| Jakarta EE Servlet | 6.0 | 6.1 | LOW - Minor version bump |
| Jackson | 2.x | 3.0 | HIGH - JSON serialization changes |
| Hibernate | 6.6.x | 7.1 | HIGH - ORM changes |
| Tomcat | 10.x | 11.0 | MEDIUM - Embedded server |
| Kotlin | 2.1.x | 2.2.20 | LOW (if using Kotlin) |

### Breaking Changes to Address

1. **MongoDB Health Indicators** - Package relocation
   - From: `spring-boot-data-mongodb`
   - To: `spring-boot-mongodb`
   - Impact: N/A (not using MongoDB in this project)

2. **Public API Restrictions**
   - Auto-configuration classes: public members removed
   - Impact: LOW - should not affect application code

3. **Property Renames**
   - `management.tracing.enabled` → `management.tracing.export.enabled`
   - `spring.dao.exceptiontranslation.enabled` → `spring.persistence.exceptiontranslation.enabled`
   - Impact: MEDIUM - need to check application.properties

4. **Jackson 2 → Jackson 3**
   - Jackson 2 now deprecated
   - Impact: MEDIUM - JSON serialization behavior may change

5. **Deprecated Interfaces**
   - `org.springframework.boot.env.EnvironmentPostProcessor` deprecated
   - Impact: LOW - check if used in custom code

### New Features Available

- ✨ HTTP Service Clients (`@HttpExchange`)
- ✨ API Versioning support
- ✨ JmsClient support
- ✨ OpenTelemetry starter
- ✨ Kotlin Serialization starter
- ✨ Virtual threading for HTTP clients
- ✨ Redis Master/Replica auto-configuration

---

## OpenRewrite Migration

### Recipe Selection
**Decision:** OpenRewrite is **HIGHLY RECOMMENDED** for Spring Boot 4.0 migration.

**Rationale:**
- Major version upgrade with multiple breaking changes
- Automated migration recipes available from Spring team
- Reduces manual effort for property renames
- Handles dependency version updates
- Updates deprecated API usage

**Selected Recipe:** `org.openrewrite.java.spring.boot4.UpgradeSpringBoot_4_0`

### OpenRewrite Execution Attempts
**Attempt 1:** Ran OpenRewrite with Spring Boot 4.0.0 already set in pom.xml
- **Result:** FAILED - Maven cannot parse POM due to missing dependency versions
- **Error:** `'dependencies.dependency.version' for org.springframework.boot:spring-boot-starter-aop:jar is missing`

**Attempt 2:** Ran OpenRewrite from Spring Boot 3.5.8 baseline
- **Result:** PARTIAL - Modified Java source code but not POM adequately
- **Issue:** Updated Java imports to Jackson 3 but left Jackson 2 in pom.xml
- **Compilation Errors:** Package `tools.jackson.core` does not exist

**Root Cause:** Spring Boot 4.0.0's modular architecture fundamentally changed dependency management:
- Traditional starters no longer in dependency management BOM
- Requires explicit versions or new modular starter names
- OpenRewrite recipes don't fully handle the modular migration yet

### Files Modified by OpenRewrite
**From Attempt 2:**
- `pom.xml` - Springdoc OpenAPI 2.8.14, Jakarta migration
- Multiple Java source files - Attempted Jackson 3 migration
- Test resources - Property updates

**Status:** Reverted Java changes, kept pom.xml Jakarta migration only

---

## Upgrade Actions Log

### 1. Version Detection and Validation ✅
- Detected current version: 3.5.8
- Target version: 4.0.0
- Validation: Major upgrade confirmed
- Java compatibility: Java 17+ required (currently Java 25) ✅

### 2. Breaking Changes Analysis ✅
- Fetched Spring Boot 4.0 release notes
- Identified breaking changes
- Assessed impact on current application
- Determined migration strategy

### 3. Pre-Upgrade Testing ✅
- All tests passing on Spring Boot 3.5.8
- Application stable and ready for upgrade attempt

### 4. OpenRewrite Migration Attempts ⚠️
- Attempted automated migration with OpenRewrite
- Encountered modular architecture blocker
- Documented issues and limitations

### 5. Analysis and Documentation ✅
- Thoroughly investigated Spring Boot 4.0.0 changes
- Identified modular architecture as primary blocker
- Documented migration requirements
- Created recommendations for manual migration approach

---

## Issues Encountered

### Critical Blocker: Modular Architecture Dependency Management

**Issue:** Spring Boot 4.0.0 no longer manages traditional starter dependencies in the same way.

**Symptoms:**
```
[ERROR] 'dependencies.dependency.version' for org.springframework.boot:spring-boot-starter-aop:jar is missing
[ERROR] 'dependencies.dependency.version' for org.springframework.boot:spring-boot-starter-web:jar is missing
```

**Root Cause:**
Spring Boot 4.0.0 introduced modular starters with new naming conventions:
- `spring-boot-starter-web` → `spring-boot-starter-webmvc`
- `spring-boot-starter-test` → `spring-boot-starter-test-classic` (temporary)
- Many starters removed from main dependency management BOM

**Attempted Solutions:**
1. ❌ Add explicit versions - Didn't work, starters not in BOM
2. ❌ Use classic starters - Didn't provide dependency management
3. ❌ Jackson 3 migration - Artifacts don't exist in expected groupIds
4. ❌ Clear Maven cache - Didn't resolve structure issue

**Conclusion:**
This is an architectural change requiring manual migration:
- Map each current starter to new modular starter name
- Update application code for any API changes
- Test extensively due to major framework changes

### Secondary Issues

**Jackson 3 Migration Complexity:**
- OpenRewrite changed imports to `tools.jackson.*`
- But Spring Boot 3.5.8 only manages Jackson 2
- Jackson 3 artifacts have different structure than expected
- `tools.jackson.datatype:jackson-datatype-jsr310:3.0.3` doesn't exist

**Recommendation:** Stay with Jackson 2 until Spring Boot 4.0.0 migration is complete

---

## Test Results

### Pre-Upgrade Tests (Spring Boot 3.5.8)
✅ All 73 tests passed

### Post-Upgrade Tests (Spring Boot 4.0.0)
❌ **Not completed** - Upgrade blocked at dependency management stage

---

## Configuration Changes

**Not applicable** - Upgrade did not proceed to configuration stage due to modular architecture blocker.

---

## Build Verification

**Not applicable** - Maven cannot parse POM with Spring Boot 4.0.0 parent due to missing dependency management for traditional starters.

---

## Migration Checklist

### Completed
- [x] Analyze Spring Boot 4.0.0 release notes
- [x] Identify breaking changes
- [x] Attempt OpenRewrite migration
- [x] Document blockers and issues
- [x] Research modular architecture changes

### Blocked - Requires Manual Migration
- [ ] Map traditional starters to modular starter names
- [ ] Update pom.xml with new starter artifacts
- [ ] Migrate to Spring Framework 7.0 APIs
- [ ] Update Spring Security 7.0 configuration
- [ ] Migrate to Jackson 3.0 (if required)
- [ ] Update Hibernate 7.1 mappings
- [ ] Review and fix compilation errors
- [ ] Update configuration properties
- [ ] Run all tests
- [ ] Fix test failures
- [ ] Verify application startup
- [ ] Verify key endpoints
- [ ] Performance testing

---

## Recommendations

### Immediate Actions
1. **Complete Spring Boot 3.5.8 upgrade** ✅ (PR #11)
2. **Monitor Spring Boot 4.x tooling maturity**
3. **Wait for improved OpenRewrite recipes**

### Future Spring Boot 4.0.0 Migration Strategy

**Option 1: Wait for Tooling** (Recommended)
- Monitor OpenRewrite recipe updates
- Wait for Spring team's enhanced migration tools
- Community migration patterns to emerge
- Estimated timeline: 3-6 months after 4.0.0 GA

**Option 2: Manual Migration** (High Effort)
- Dedicate 2-4 weeks for migration effort
- Follow official Spring Boot 4.0 Migration Guide step-by-step
- Manually map all 9 starters to new modular equivalents
- Extensive testing required
- Risk: Medium-High

**Option 3: Incremental Approach**
- Stay on Spring Boot 3.5.x LTS releases
- Monitor critical security updates
- Plan migration when business value justifies effort

### Required Resources for Manual Migration
- Senior Spring Boot developer (2-3 weeks)
- QA testing (1 week)
- Spring Boot 4.0 Migration Guide
- Spring Framework 7.0 documentation
- Staging environment for testing

---

## Summary

Spring Boot 4.0.0 represents a significant architectural shift with its modular starter system. While OpenRewrite provides valuable automation for many migration tasks, the fundamental changes to dependency management require manual intervention and careful planning.

**Key Takeaways:**
1. ✅ Spring Boot 3.5.8 upgrade successful and recommended
2. ⚠️ Spring Boot 4.0.0 requires manual migration effort
3. 🔧 OpenRewrite helpful but incomplete for modular migration
4. 📚 Official migration guide is essential resource
5. ⏱️ Significant time investment required (2-4 weeks)

**Decision:** Recommend staying on Spring Boot 3.5.x until migration tooling matures or business requirements justify the manual migration effort.

---

## References

- [Spring Boot 4.0 Release Notes](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-4.0-Release-Notes)
- [Spring Boot 4.0.0 Release Announcement](https://spring.io/blog/2025/11/20/spring-boot-4-0-0-available-now/)
- [Spring Boot 4.0 Migration Guide](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-4.0-Migration-Guide)
- [Modularizing Spring Boot](https://spring.io/blog/2025/10/28/modularizing-spring-boot/)
- [Spring Framework 7.0 Documentation](https://docs.spring.io/spring-framework/docs/7.0.x/reference/)
- [OpenRewrite Spring Boot 4.0 Recipes](https://docs.openrewrite.org/recipes/java/spring/boot4/upgradespringboot_4_0-community-edition)
