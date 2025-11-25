# Java Upgrade Decision and Process Log

## Decision Summary
- **Language**: Java
- **Current Version**: 17 (LTS)
- **Target Version**: 25 (LTS)
- **Decision**: Upgrade to Java 25
- **Decision Date**: 2025-11-25

## Rationale

### Support Timeline Analysis

**Java 17 (Current Version):**
- Released: September 2021
- LTS Status: Yes (Long-Term Support)
- Support End: September 2029 (8 years of support)
- Current Status: Still well-supported, widely adopted in production

**Java 21:**
- Released: September 2023
- LTS Status: Yes
- Support End: September 2031
- Current Status: Battle-tested, widely adopted, stable

**Java 25 (Target Version):**
- Released: September 2025
- LTS Status: Yes
- Support End: September 2033
- Current Status: Latest LTS, cutting-edge features

### Version Selection Logic

**Key Decision Factors:**

1. **Branch Name Hint**: The current branch is `java25-skill-update`, which strongly suggests an intention to upgrade to Java 25.

2. **Local Environment**: The local development environment is running Java 25:
   ```
   openjdk version "25" 2025-09-16
   OpenJDK Runtime Environment (build 25+36-3489)
   ```

3. **Project Type**: This is the Spring PetClinic REST sample application - a learning/demonstration project, not a critical production application. This makes it suitable for adopting the latest LTS version.

4. **Framework Compatibility**:
   - Spring Boot 3.5.7 (current version in pom.xml) supports Java 25
   - All dependencies appear to be recent and compatible

5. **Current Version Status**: Java 17 is still well-supported until 2029, so this is not a forced upgrade due to EOL concerns.

**Decision Logic Applied:**
```
IF (branch hints at version OR local env matches newer version) AND (sample/non-critical project):
  → Choose hinted/matching LTS version (Java 25)
```

### Why Java 25 Over Java 21?

While Java 21 would be a more conservative choice for production systems, Java 25 is appropriate here because:

1. **Explicit Intent**: Branch name suggests Java 25 is the target
2. **Local Environment Alignment**: Developer already has Java 25 installed
3. **Sample Application**: This is a demonstration/learning project, not critical infrastructure
4. **Latest LTS Benefits**:
   - Longest support timeline (until 2033)
   - Latest performance improvements
   - Modern language features
   - Best alignment with future Spring Boot versions

### Risk Assessment

**Low Risk Upgrade:**
- Spring Boot 3.5.7 is recent and should fully support Java 25
- All dependencies are modern versions
- No deprecated Java 8-style APIs detected in the build configuration
- CI/CD uses GitHub Actions which supports Java 25
- Docker base images available (eclipse-temurin:25)

**Potential Challenges:**
- None identified - Spring Boot 3.x is designed for Java 17+ and should handle Java 25 without issues

## Actions Taken

### 1. Initial Analysis
- Examined pom.xml: No explicit Java version property found (inherits from Spring Boot parent)
- Checked GitHub Actions workflows: All currently use Java 17
- Verified local environment: Java 25 already installed
- No Dockerfile found in repository (uses Jib for Docker image builds)

### 2. Version Reference Updates

#### Files Updated:

**1. `pom.xml`**
- Added explicit `<maven.compiler.release>25</maven.compiler.release>` property
- The Spring Boot parent (3.5.7) was setting this to 17, so we override it
- Location: In `<properties>` section at line 23

**2. `.github/workflows/maven-build-master.yml`**
- Changed: `java-version: '17'` → `java-version: '25'`
- Changed: Step name from "Set up JDK 17" → "Set up JDK 25"
- Location: Lines 17-20

**3. `.github/workflows/maven-build-pull-request.yml`**
- Changed: `java-version: '17'` → `java-version: '25'`
- Changed: Step name from "Set up JDK 17" → "Set up JDK 25"
- Location: Lines 14-17

**4. `.github/workflows/docker-build.yml`**
- Changed: `java-version: '17'` → `java-version: '25'`
- Changed: Step name from "Set up JDK 17" → "Set up JDK 25"
- Updated: `actions/setup-java@v3` → `actions/setup-java@v4` (for better Java 25 support)
- Location: Lines 21-25

**5. `.github/workflows/newman-pipeline.yml`**
- Changed: `java-version: '17'` → `java-version: '25'`
- Changed: Step name from "Set up JDK 17" → "Set up JDK 25"
- Location: Lines 15-18

**Summary**: All version references have been updated from Java 17 to Java 25 across the entire project.

### 3. Build and Test Results

**Build Status: ✅ SUCCESS**

#### Clean Compile Results:
```
[INFO] --- compiler:3.14.1:compile (default-compile) @ spring-petclinic-rest ---
[INFO] Recompiling the module because of changed source code.
[INFO] Compiling 107 source files with javac [debug parameters release 25] to target\classes
[INFO] BUILD SUCCESS
[INFO] Total time:  53.324 s
```

#### Test Results:
```
[INFO] Tests run: 216, Failures: 0, Errors: 0, Skipped: 0
[INFO] BUILD SUCCESS
[INFO] Total time:  03:08 min
```

**All 216 tests passed successfully!**

Test suites executed:
- ValidatorTests: 1 test
- OwnerRestControllerTests: 22 tests
- PetRestControllerTests: 8 tests
- PetTypeRestControllerTests: 12 tests
- SpecialtyRestControllerTests: 10 tests
- UserRestControllerTests: (tests included)
- VetRestControllerTests: (tests included)
- VisitRestControllerTests: (tests included)
- ApplicationTestCase: (tests included)
- SpringConfigTests: 1 test

**Application Startup**: Verified - Spring Boot 3.5.7 starts successfully with Java 25

#### Warnings Observed:

**Java 25 Warnings (Expected):**
These warnings are from Maven's dependencies and are not blockers:

1. **Restricted Methods Warning**:
   ```
   WARNING: A restricted method in java.lang.System has been called
   WARNING: java.lang.System::load has been called by org.fusesource.jansi.internal.JansiLoader
   WARNING: Restricted methods will be blocked in a future release unless native access is enabled
   ```
   - **Impact**: Low - Maven library (jansi) uses restricted API
   - **Action Required**: None for our project. Maven will need to update jansi in future

2. **Deprecated Unsafe Warning**:
   ```
   WARNING: A terminally deprecated method in sun.misc.Unsafe has been called
   WARNING: sun.misc.Unsafe::objectFieldOffset has been called by Guava
   ```
   - **Impact**: Low - Maven library (Guava) uses deprecated API
   - **Action Required**: None for our project. Maven will need to update Guava in future

**Application Warnings (Normal):**
```
WARNING: No application/json content media type found in response
```
- **Impact**: None - OpenAPI Generator warning, not related to Java version

### 4. Issues Encountered

**No Issues Encountered!**

The upgrade from Java 17 to Java 25 completed smoothly with:
- ✅ All configuration files updated successfully
- ✅ Clean build completed without errors
- ✅ All 216 tests passed (0 failures, 0 errors, 0 skipped)
- ✅ Application starts correctly
- ✅ Spring Boot 3.5.7 fully compatible with Java 25
- ✅ All dependencies compatible

The only warnings are from Maven's own dependencies (jansi and Guava) using restricted/deprecated APIs in Java 25. These warnings do not affect the PetClinic application and will be addressed by Maven in future releases.

## Final Recommendation

**✅ UPGRADE SUCCESSFUL - READY TO MERGE**

### Summary

The upgrade from Java 17 to Java 25 has been **successfully completed** with zero issues. All 216 tests pass, the application builds and runs correctly, and the project is now using the latest LTS version of Java with support until 2033.

### What Was Changed

**Files Modified: 6**
1. `pom.xml` - Added `maven.compiler.release=25` property
2. `.github/workflows/maven-build-master.yml` - Updated to JDK 25
3. `.github/workflows/maven-build-pull-request.yml` - Updated to JDK 25
4. `.github/workflows/docker-build.yml` - Updated to JDK 25 and setup-java@v4
5. `.github/workflows/newman-pipeline.yml` - Updated to JDK 25
6. `UPGRADE_EXPLANATION.md` - This decision and process documentation (NEW)

### Validation Results

- ✅ **Build**: SUCCESS (107 source files compiled with release 25)
- ✅ **Tests**: 216/216 passed (0 failures, 0 errors, 0 skipped)
- ✅ **Application Startup**: Verified working with Spring Boot 3.5.7
- ✅ **CI/CD**: All workflows updated and ready for GitHub Actions
- ✅ **Dependencies**: All compatible with Java 25

### Benefits of This Upgrade

1. **Extended Support**: Java 25 LTS supported until September 2033 (vs Java 17 until 2029)
2. **Latest Features**: Access to all Java 18-25 language and performance improvements
3. **Developer Alignment**: Matches local development environment (Java 25)
4. **Future-Proof**: Best positioned for upcoming Spring Boot releases
5. **Sample Project Showcase**: Demonstrates latest LTS capabilities

### Known Warnings (Non-Blocking)

Minor warnings from Maven's dependencies (jansi, Guava) using restricted/deprecated APIs. These:
- Do not affect the application
- Are normal for Java 25
- Will be fixed by Maven in future releases
- Require no action from this project

### Follow-Up Actions

**Immediate**:
- ✅ Commit all changes
- ✅ Push to remote repository
- ✅ Create pull request
- ⏳ Wait for CI/CD to run (all workflows will use Java 25)

**Post-Merge**:
- Monitor CI/CD builds to ensure GitHub Actions work with Java 25
- Update any developer documentation mentioning Java requirements
- Notify team members to upgrade local JDK to 25

### Conclusion

This upgrade is **recommended for immediate merge**. The transition from Java 17 to Java 25 is smooth, all tests pass, and the project benefits from the latest LTS version with extended support timeline. The sample/demonstration nature of this project makes it an ideal showcase for Java 25 capabilities.
