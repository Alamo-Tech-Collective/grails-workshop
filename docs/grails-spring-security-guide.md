# Grails Spring Security Core Guide

## Overview

This guide covers setting up authentication and authorization in a Grails 6.2.3 application using Spring Security Core plugin version 6.1.2.

## Prerequisites

- Grails 6.2.3 application created
- Java 11 (Temurin)
- IntelliJ IDEA with Grails plugin

---

## Step 1: Add Spring Security Core Dependency

Open your `build.gradle` file and add the Spring Security Core plugin to the dependencies section:

```gradle
dependencies {
    // ... other dependencies ...
    
    implementation "org.grails.plugins:spring-security-core:6.1.2"
}
```

Save the file and let Gradle sync the dependencies.

---

## Step 2: Generate Security Domain Classes

Run the Spring Security quickstart command to generate the necessary domain classes and configuration:

```bash
./gradlew runCommand "-Pargs=s2-quickstart workshop User Role"
```

**Command breakdown:**
- `s2-quickstart` - Spring Security quickstart command
- `workshop` - Package name for generated classes
- `User` - Name of the user domain class
- `Role` - Name of the role/authority domain class

This command will generate several files:

### Generated Domain Classes

1. **grails-app/domain/workshop/User.groovy**
   ```groovy
   package workshop
   
   class User implements Serializable {
       String username
       String password
       boolean enabled = true
       boolean accountExpired
       boolean accountLocked
       boolean passwordExpired
       
       static constraints = {
           username blank: false, unique: true
           password blank: false
       }
       
       static mapping = {
           password column: '`password`'
       }
   }
   ```

2. **grails-app/domain/workshop/Role.groovy**
   ```groovy
   package workshop
   
   class Role implements Serializable {
       String authority
       
       static constraints = {
           authority blank: false, unique: true
       }
   }
   ```

3. **grails-app/domain/workshop/UserRole.groovy**
   ```groovy
   package workshop
   
   class UserRole implements Serializable {
       User user
       Role role
       
       static mapping = {
           id composite: ['user', 'role']
           version false
       }
   }
   ```

### Generated Configuration

The command also adds security configuration to `grails-app/conf/application.groovy`:

```groovy
grails.plugin.springsecurity.userLookup.userDomainClassName = 'workshop.User'
grails.plugin.springsecurity.userLookup.authorityJoinClassName = 'workshop.UserRole'
grails.plugin.springsecurity.authority.className = 'workshop.Role'
grails.plugin.springsecurity.controllerAnnotations.staticRules = [
    [pattern: '/',               access: ['permitAll']],
    [pattern: '/error',          access: ['permitAll']],
    [pattern: '/index',          access: ['permitAll']],
    [pattern: '/index.gsp',      access: ['permitAll']],
    [pattern: '/shutdown',       access: ['permitAll']],
    [pattern: '/assets/**',      access: ['permitAll']],
    [pattern: '/**/js/**',       access: ['permitAll']],
    [pattern: '/**/css/**',      access: ['permitAll']],
    [pattern: '/**/images/**',   access: ['permitAll']],
    [pattern: '/**/favicon.ico', access: ['permitAll']]
]

grails.plugin.springsecurity.filterChain.chainMap = [
    [pattern: '/assets/**',      filters: 'none'],
    [pattern: '/**/js/**',       filters: 'none'],
    [pattern: '/**/css/**',      filters: 'none'],
    [pattern: '/**/images/**',   filters: 'none'],
    [pattern: '/**/favicon.ico', filters: 'none'],
    [pattern: '/**',             filters: 'JOINED_FILTERS']
]
```

---

## Step 3: Create Initial Users and Roles

Create a Bootstrap configuration to set up initial users and roles. Open `grails-app/init/workshop/BootStrap.groovy`:

```groovy
package workshop

class BootStrap {

    def init = { servletContext ->
        createUsersAndRoles()
    }
    
    def destroy = {
    }
    
    private void createUsersAndRoles() {
        // Create roles
        def adminRole = Role.findOrSaveByAuthority('ROLE_ADMIN')
        def userRole = Role.findOrSaveByAuthority('ROLE_USER')
        
        // Create admin user
        def adminUser = User.findOrSaveByUsername('admin')
        if (!adminUser.id) {
            adminUser = new User(
                username: 'admin',
                password: 'admin123',
                enabled: true
            ).save(flush: true)
        }
        
        // Create regular user
        def normalUser = User.findOrSaveByUsername('user')
        if (!normalUser.id) {
            normalUser = new User(
                username: 'user',
                password: 'user123',
                enabled: true
            ).save(flush: true)
        }
        
        // Assign roles
        if (!UserRole.exists(adminUser.id, adminRole.id)) {
            UserRole.create(adminUser, adminRole, true)
        }
        
        if (!UserRole.exists(normalUser.id, userRole.id)) {
            UserRole.create(normalUser, userRole, true)
        }
        
        println "Users created:"
        println "  - admin/admin123 (ROLE_ADMIN)"
        println "  - user/user123 (ROLE_USER)"
    }
}
```

## Security Annotations Reference

### @Secured
Restrict access to controllers or actions:

```groovy
// Single role
@Secured('ROLE_ADMIN')

// Multiple roles (OR condition)
@Secured(['ROLE_ADMIN', 'ROLE_USER'])

// Expression-based
@Secured(["hasRole('ROLE_ADMIN') and hasIpAddress('192.168.1.0/24')"])

// Anonymous access
@Secured('IS_AUTHENTICATED_ANONYMOUSLY')

// Any authenticated user
@Secured('IS_AUTHENTICATED_FULLY')
```

### Security Service Methods

Inject `springSecurityService` to access:

```groovy
def springSecurityService

// Get current user
def user = springSecurityService.currentUser

// Get principal
def principal = springSecurityService.principal

// Check authentication
if (springSecurityService.isLoggedIn()) {
    // User is authenticated
}

// Get user authorities
def authorities = springSecurityService.authentication.authorities
```

---

## Troubleshooting

### Issue: Login always fails
- Check password encoding matches between Bootstrap and configuration
- Verify users are being created in Bootstrap
- Check database for user records

### Issue: Access denied after login
- Verify UserRole entries exist in database
- Check role names match exactly (ROLE_ prefix)
- Enable debug logging:
  ```groovy
  logging.level.org.springframework.security = 'DEBUG'
  ```
---

## Resources

- [Spring Security Core Plugin Documentation](https://grails.github.io/grails-spring-security-core/)
- [Spring Security Reference](https://docs.spring.io/spring-security/reference/)
- [Grails Security Guide](https://docs.grails.org/latest/guide/security.html)