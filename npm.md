# NPM (Node Package Manager) - Complete Guide

## Table of Contents
1. [Fundamentals](#fundamentals)
2. [Package Management](#package-management)
3. [Versioning & Dependencies](#versioning--dependencies)
4. [NPM Scripts](#npm-scripts)
5. [Publishing Packages](#publishing-packages)
6. [Security & Best Practices](#security--best-practices)
7. [Advanced Topics](#advanced-topics)
8. [Troubleshooting](#troubleshooting)

---

## Fundamentals

### Q1: What is NPM?

**Answer:**
NPM (Node Package Manager) is the default package manager for Node.js. It's a command-line tool and registry for managing JavaScript packages.

**Key Features:**
- **Package Registry**: Largest ecosystem of open-source libraries
- **Dependency Management**: Automatically handles project dependencies
- **Version Control**: Manages package versions and updates
- **Scripts**: Run custom build and development tasks
- **Publishing**: Share your own packages with the community

**What NPM Does:**
```bash
# 1. Install packages
npm install express

# 2. Manage dependencies
npm install
npm update

# 3. Run scripts
npm start
npm test
npm run build

# 4. Publish packages
npm publish

# 5. Manage versions
npm version patch
```

**NPM vs Yarn vs pnpm:**

| Feature | NPM | Yarn | pnpm |
|---------|-----|------|------|
| Speed | Moderate | Fast | Fastest |
| Disk Space | More | More | Less (hardlinks) |
| Lock File | package-lock.json | yarn.lock | pnpm-lock.yaml |
| Workspaces | ✅ Yes | ✅ Yes | ✅ Yes |
| Default for Node.js | ✅ Yes | ❌ No | ❌ No |

### Q2: What is package.json?

**Answer:**
`package.json` is the manifest file for your Node.js project. It contains metadata, dependencies, scripts, and configuration.

**Basic structure:**
```json
{
  "name": "my-project",
  "version": "1.0.0",
  "description": "A sample project",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "test": "jest",
    "build": "webpack",
    "dev": "nodemon index.js"
  },
  "keywords": ["example", "npm"],
  "author": "Your Name <you@example.com>",
  "license": "MIT",
  "dependencies": {
    "express": "^4.18.2",
    "lodash": "^4.17.21"
  },
  "devDependencies": {
    "nodemon": "^3.0.1",
    "jest": "^29.5.0"
  },
  "engines": {
    "node": ">=18.0.0",
    "npm": ">=9.0.0"
  },
  "repository": {
    "type": "git",
    "url": "https://github.com/user/repo.git"
  }
}
```

**Key Fields:**

**Required:**
- `name`: Package name (lowercase, no spaces)
- `version`: Semantic version (e.g., 1.0.0)

**Common:**
- `description`: Brief description
- `main`: Entry point file
- `scripts`: Custom commands
- `dependencies`: Production packages
- `devDependencies`: Development-only packages
- `author`: Creator information
- `license`: License type (MIT, ISC, etc.)

**Generate package.json:**
```bash
# Interactive creation
npm init

# With defaults
npm init -y

# Scoped package
npm init @scope/package-name
```

### Q3: What is the difference between dependencies and devDependencies?

**Answer:**

**dependencies:**
- Required for the application to run in production
- Installed in production environments
- Users of your package will also install these

```json
{
  "dependencies": {
    "express": "^4.18.2",
    "mongoose": "^7.0.3",
    "dotenv": "^16.0.3"
  }
}
```

```bash
# Install as dependency
npm install express
npm install --save express  # --save is default
npm i express               # shorthand
```

**devDependencies:**
- Only needed during development
- NOT installed in production
- Testing, building, linting tools

```json
{
  "devDependencies": {
    "nodemon": "^3.0.1",
    "jest": "^29.5.0",
    "eslint": "^8.40.0",
    "webpack": "^5.80.0"
  }
}
```

```bash
# Install as devDependency
npm install --save-dev jest
npm install -D jest        # shorthand
```

**Production installation:**
```bash
# Skip devDependencies (production)
npm install --production
npm install --omit=dev

# CI/CD environments
NODE_ENV=production npm install
```

**When to use which:**

| Dependency Type | Use For | Examples |
|----------------|---------|----------|
| dependencies | Runtime code | express, react, lodash |
| devDependencies | Development tools | jest, webpack, eslint |
| peerDependencies | Plugin requirements | react (for React plugins) |
| optionalDependencies | Nice-to-have | Image optimization libs |

### Q4: What is package-lock.json?

**Answer:**
`package-lock.json` is an auto-generated file that locks exact versions of all installed packages and their dependencies.

**Purpose:**
- **Reproducible builds**: Same versions across all environments
- **Faster installs**: NPM knows exact versions to fetch
- **Integrity checks**: Verifies package hasn't been tampered with
- **Dependency tree**: Complete snapshot of node_modules

**Example:**
```json
{
  "name": "my-project",
  "version": "1.0.0",
  "lockfileVersion": 3,
  "requires": true,
  "packages": {
    "": {
      "name": "my-project",
      "version": "1.0.0",
      "dependencies": {
        "express": "^4.18.2"
      }
    },
    "node_modules/express": {
      "version": "4.18.2",
      "resolved": "https://registry.npmjs.org/express/-/express-4.18.2.tgz",
      "integrity": "sha512-5/PsL6iGPdfQ/lKM1UuielYgv3BUoJfz1aUwU9vHZ+J7gyvwdQXFEBIEIaxeGf0GIcreATNyBExtalisDbuMqQ==",
      "dependencies": {
        "accepts": "~1.3.8",
        "body-parser": "1.20.1"
      }
    }
  }
}
```

**Should you commit it?**
```bash
# ✅ YES - Commit package-lock.json
git add package-lock.json
git commit -m "Update dependencies"

# Why?
# - Ensures everyone gets same versions
# - Prevents "works on my machine" issues
# - Required for reproducible deployments
```

**Common operations:**
```bash
# Generate/update package-lock.json
npm install

# Install from lock file only (CI/CD)
npm ci

# Update lock file
npm update

# Fix corrupted lock file
rm package-lock.json
npm install
```

---

## Package Management

### Q5: How do you install packages with NPM?

**Answer:**

**Install from package.json:**
```bash
# Install all dependencies
npm install
npm i              # shorthand

# Install only production dependencies
npm install --production
npm install --omit=dev

# Clean install (deletes node_modules first)
npm ci             # Use in CI/CD
```

**Install specific packages:**
```bash
# Latest version
npm install express
npm i express

# Specific version
npm install express@4.18.2

# Version range
npm install express@^4.0.0
npm install express@~4.18.0

# Install from git
npm install user/repo
npm install git+https://github.com/user/repo.git
npm install git+ssh://git@github.com/user/repo.git#branch

# Install from tarball
npm install ./package.tgz
npm install https://example.com/package.tgz

# Multiple packages at once
npm install express mongoose dotenv
```

**Install types:**
```bash
# Production dependency
npm install express

# Development dependency
npm install -D jest
npm install --save-dev jest

# Global package (CLI tools)
npm install -g nodemon
npm install --global typescript

# Optional dependency
npm install --save-optional sharp

# Peer dependency (manual)
npm install --save-peer react
```

**Install specific scope:**
```bash
# Scoped package
npm install @types/node
npm install @angular/core

# From specific registry
npm install express --registry=https://registry.npmjs.org
```

### Q6: How do you uninstall packages?

**Answer:**

```bash
# Uninstall and remove from package.json
npm uninstall express
npm uninstall lodash mongoose

# Uninstall devDependency
npm uninstall -D jest

# Uninstall global package
npm uninstall -g nodemon

# Just remove from node_modules (not package.json)
npm uninstall --no-save express

# Remove unused packages
npm prune

# Remove devDependencies
npm prune --production
```

### Q7: How do you update packages?

**Answer:**

**Update packages:**
```bash
# Check for outdated packages
npm outdated

# Output example:
# Package    Current  Wanted  Latest  Location
# express    4.17.1   4.18.2  4.18.2  my-project
# lodash     4.17.20  4.17.21 4.17.21 my-project

# Update all packages to "Wanted" version
npm update
npm update --save  # Update package.json too

# Update specific package
npm update express

# Update to latest (ignoring semver)
npm install express@latest

# Update all to latest
npm update --latest  # npm 8+

# Update global packages
npm update -g

# Interactive update (with npm-check-updates)
npx npm-check-updates -i
```

**Using npm-check-updates:**
```bash
# Install globally
npm install -g npm-check-updates

# Check what can be updated
ncu

# Update package.json
ncu -u

# Then install
npm install

# Interactive mode
ncu -i

# Update only patch/minor
ncu --target minor
```

### Q8: What are NPM scripts?

**Answer:**
NPM scripts are commands defined in `package.json` that automate common tasks.

**Basic scripts:**
```json
{
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js",
    "test": "jest",
    "build": "webpack --mode production",
    "lint": "eslint .",
    "format": "prettier --write \"**/*.js\""
  }
}
```

```bash
# Run scripts
npm start          # npm start (special)
npm test           # npm test or npm t (special)
npm run dev        # npm run <script>
npm run build
npm run lint
```

**Pre and post hooks:**
```json
{
  "scripts": {
    "prebuild": "rm -rf dist",
    "build": "webpack",
    "postbuild": "echo 'Build complete!'",
    
    "pretest": "npm run lint",
    "test": "jest",
    "posttest": "echo 'Tests finished'"
  }
}
```

```bash
# Running npm run build executes:
# 1. prebuild
# 2. build
# 3. postbuild
```

**Passing arguments:**
```json
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage"
  }
}
```

```bash
# Pass extra arguments with --
npm test -- --verbose
npm run build -- --mode=development
```

**Environment variables:**
```json
{
  "scripts": {
    "start": "NODE_ENV=production node server.js",
    "dev": "NODE_ENV=development nodemon server.js",
    
    // Cross-platform (use cross-env)
    "start:cross": "cross-env NODE_ENV=production node server.js"
  }
}
```

**Complex scripts:**
```json
{
  "scripts": {
    // Run multiple commands sequentially
    "build": "npm run clean && npm run compile && npm run bundle",
    
    // Run in parallel
    "dev": "npm run dev:server & npm run dev:client",
    
    // Better parallel (use npm-run-all)
    "dev:all": "npm-run-all --parallel dev:*",
    
    // Conditionals
    "deploy": "npm run test && npm run build && npm run upload"
  }
}
```

**Common patterns:**
```json
{
  "scripts": {
    "clean": "rm -rf dist",
    "prebuild": "npm run clean",
    "build": "webpack --mode production",
    "postbuild": "npm run size",
    
    "dev": "nodemon --exec npm start",
    "start": "node server.js",
    
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    
    "lint": "eslint .",
    "lint:fix": "eslint . --fix",
    
    "format": "prettier --write \"**/*.{js,json,md}\"",
    "format:check": "prettier --check \"**/*.{js,json,md}\"",
    
    "prepare": "husky install",
    "prepublishOnly": "npm run test && npm run build"
  }
}
```

---

## Versioning & Dependencies

### Q9: What is Semantic Versioning (Semver)?

**Answer:**
Semantic Versioning uses a three-part version number: **MAJOR.MINOR.PATCH**

**Format: X.Y.Z**
- **MAJOR (X)**: Breaking changes (incompatible API)
- **MINOR (Y)**: New features (backward compatible)
- **PATCH (Z)**: Bug fixes (backward compatible)

**Examples:**
```
1.0.0 → Initial release
1.0.1 → Bug fix (patch)
1.1.0 → New feature (minor)
2.0.0 → Breaking change (major)
```

**Version prefixes:**
```json
{
  "dependencies": {
    "express": "4.18.2",      // Exact version
    "lodash": "^4.17.21",     // Compatible (minor/patch updates)
    "react": "~18.2.0",       // Approximately (patch updates only)
    "vue": "*",               // Any version (not recommended)
    "axios": ">=1.0.0",       // Greater than or equal
    "next": "13.x",           // Any 13.x.x version
    "typescript": "latest"    // Latest version
  }
}
```

**Caret (^) - Default:**
```
^1.2.3 → >=1.2.3 <2.0.0   // Allow minor and patch
^0.2.3 → >=0.2.3 <0.3.0   // For 0.x, only patch
^0.0.3 → >=0.0.3 <0.0.4   // For 0.0.x, exact
```

**Tilde (~) - Patch updates only:**
```
~1.2.3 → >=1.2.3 <1.3.0   // Allow patch only
~1.2   → >=1.2.0 <1.3.0
~1     → >=1.0.0 <2.0.0
```

**Range syntax:**
```json
{
  "dependencies": {
    "package1": ">=1.0.0 <2.0.0",    // Range
    "package2": "1.0.0 - 1.5.0",      // Between
    "package3": "1.x || 2.x",         // OR
    "package4": ">1.0.0 <=2.0.0"      // Compound
  }
}
```

**Best practices:**
- ✅ Use `^` for most dependencies (default)
- ✅ Use exact versions for critical packages
- ❌ Avoid `*` and `latest` in production
- ✅ Lock versions with package-lock.json

### Q10: How do you manage package versions?

**Answer:**

**View package versions:**
```bash
# Current version
npm version

# Specific package version
npm list express
npm ls express

# All packages
npm list
npm list --depth=0  # Top-level only

# Check outdated
npm outdated

# View package info
npm view express
npm info express
npm show express version
```

**Update version in package.json:**
```bash
# Bump patch (1.0.0 → 1.0.1)
npm version patch

# Bump minor (1.0.0 → 1.1.0)
npm version minor

# Bump major (1.0.0 → 2.0.0)
npm version major

# Prerelease (1.0.0 → 1.0.1-0)
npm version prerelease

# Specific version
npm version 2.0.0

# With git tag
npm version patch -m "Upgrade to %s"

# Without git tag
npm version patch --no-git-tag-version
```

**Version tags:**
```bash
# Install specific tag
npm install express@latest
npm install react@next
npm install vue@beta

# Publish with tag
npm publish --tag beta
npm publish --tag next

# View available tags
npm dist-tag ls express
```

### Q11: What are peer dependencies?

**Answer:**
Peer dependencies specify which versions of a package your package is compatible with, but doesn't install them.

**Use case: Plugins and extensions**

**Example - React plugin:**
```json
{
  "name": "react-custom-hook",
  "version": "1.0.0",
  "peerDependencies": {
    "react": ">=16.8.0",
    "react-dom": ">=16.8.0"
  },
  "devDependencies": {
    "react": "^18.2.0",      // For development
    "react-dom": "^18.2.0"
  }
}
```

**Why peer dependencies?**
- Prevent multiple versions of the same package
- Let the host app control the version
- Reduce bundle size

**NPM behavior:**

**NPM 3-6:**
- Warning if peer dependency not met
- Developer must install manually

**NPM 7+:**
- Automatically installs peer dependencies
- Use `--legacy-peer-deps` for old behavior

```bash
# Install without peer dependencies
npm install --legacy-peer-deps

# Force install despite conflicts
npm install --force
```

**Peer dependency warnings:**
```bash
npm WARN react-custom-hook@1.0.0 requires a peer of react@>=16.8.0 but none is installed.
```

---

## NPM Scripts

### Q12: What are lifecycle scripts?

**Answer:**
Lifecycle scripts are automatically executed at specific times during package installation and publishing.

**Common lifecycle scripts:**

```json
{
  "scripts": {
    // Pre-install
    "preinstall": "echo 'Before package installation'",
    
    // Install
    "install": "node-gyp rebuild",
    
    // Post-install
    "postinstall": "node setup.js",
    
    // Pre-publish
    "prepublishOnly": "npm run test && npm run build",
    
    // Publish
    "publish": "echo 'Publishing'",
    
    // Post-publish
    "postpublish": "echo 'Published successfully'",
    
    // Pre/post for any script
    "prebuild": "npm run clean",
    "build": "webpack",
    "postbuild": "echo 'Build complete'",
    
    // Prepare (runs before pack/publish and on local install)
    "prepare": "husky install"
  }
}
```

**Full lifecycle order:**

**On `npm install`:**
1. `preinstall`
2. `install`
3. `postinstall`
4. `prepublish` (deprecated)
5. `preprepare`
6. `prepare`
7. `postprepare`

**On `npm publish`:**
1. `prepublishOnly`
2. `prepack`
3. `prepare`
4. `postpack`
5. `publish`
6. `postpublish`

**Real-world examples:**

**1. Build before publish:**
```json
{
  "scripts": {
    "prepublishOnly": "npm run test && npm run build",
    "build": "tsc"
  }
}
```

**2. Setup Git hooks:**
```json
{
  "scripts": {
    "prepare": "husky install"
  }
}
```

**3. Post-install setup:**
```json
{
  "scripts": {
    "postinstall": "patch-package"
  }
}
```

**4. Clean before build:**
```json
{
  "scripts": {
    "prebuild": "rm -rf dist",
    "build": "webpack"
  }
}
```

---

## Publishing Packages

### Q13: How do you publish a package to NPM?

**Answer:**

**Step 1: Create NPM account**
```bash
# Create account on npmjs.com or via CLI
npm adduser
# or
npm login
```

**Step 2: Prepare package.json**
```json
{
  "name": "my-awesome-package",
  "version": "1.0.0",
  "description": "A great package",
  "main": "index.js",
  "author": "Your Name",
  "license": "MIT",
  "keywords": ["awesome", "package"],
  "repository": {
    "type": "git",
    "url": "https://github.com/user/repo.git"
  },
  "files": [
    "dist",
    "README.md",
    "LICENSE"
  ]
}
```

**Step 3: Test package locally**
```bash
# Pack the package
npm pack

# Creates: my-awesome-package-1.0.0.tgz

# Test in another project
cd /path/to/test-project
npm install /path/to/my-awesome-package-1.0.0.tgz

# Or use npm link
cd /path/to/my-package
npm link

cd /path/to/test-project
npm link my-awesome-package
```

**Step 4: Publish**
```bash
# Dry run (see what will be published)
npm publish --dry-run

# Publish to NPM
npm publish

# Publish scoped package (public)
npm publish --access public

# Publish with specific tag
npm publish --tag beta
```

**Step 5: Verify**
```bash
# Check on NPM
npm view my-awesome-package

# Install and test
npm install my-awesome-package
```

**Update published package:**
```bash
# Bump version
npm version patch  # 1.0.0 → 1.0.1

# Publish new version
npm publish
```

**Unpublish (within 72 hours):**
```bash
# Unpublish specific version
npm unpublish my-package@1.0.0

# Unpublish entire package (use carefully!)
npm unpublish my-package --force
```

### Q14: What is .npmignore and how does it work?

**Answer:**
`.npmignore` specifies files to exclude when publishing to NPM.

**Default behavior:**
- If `.npmignore` exists, it's used
- If not, `.gitignore` is used
- Some files are always included/excluded

**Example .npmignore:**
```
# Development files
src/
tests/
*.test.js
*.spec.js

# Config files
.eslintrc
.prettierrc
tsconfig.json
jest.config.js

# CI/CD
.github/
.gitlab-ci.yml
.travis.yml

# Documentation (if large)
docs/
examples/

# Build artifacts
*.log
.DS_Store
node_modules/
coverage/

# Don't exclude:
# dist/
# lib/
# index.js
# README.md
# LICENSE
```

**Always excluded (automatic):**
```
node_modules/
package-lock.json
.git/
.*.swp
._*
.DS_Store
npm-debug.log
```

**Always included (even if ignored):**
```
package.json
README (any case/extension)
LICENSE / LICENCE (any case/extension)
Main file (from package.json "main")
```

**Using "files" field (recommended):**
```json
{
  "name": "my-package",
  "files": [
    "dist",
    "lib",
    "index.js",
    "README.md"
  ]
}
```

**Check what will be published:**
```bash
# Dry run
npm publish --dry-run

# Create tarball and inspect
npm pack
tar -tzf my-package-1.0.0.tgz
```

### Q15: How do you create a scoped package?

**Answer:**
Scoped packages are namespaced packages (e.g., `@username/package-name`).

**Benefits:**
- Unique namespace (no name conflicts)
- Group related packages
- Can be private or public

**Create scoped package:**
```json
{
  "name": "@myusername/my-package",
  "version": "1.0.0",
  "description": "My scoped package"
}
```

**Publish scoped package:**
```bash
# Public scoped package (free)
npm publish --access public

# Private scoped package (requires paid account)
npm publish  # Default is private for scoped packages
```

**Install scoped package:**
```bash
npm install @myusername/my-package
```

**Use in code:**
```javascript
const myPackage = require('@myusername/my-package');
// or
import myPackage from '@myusername/my-package';
```

**Organization scopes:**
```bash
# Create organization on npmjs.com
# Then publish under org scope
npm publish --access public
```

**Example scopes:**
```
@angular/core
@babel/preset-env
@types/node
@mycompany/internal-tool
```

---

## Security & Best Practices

### Q16: How do you check for security vulnerabilities?

**Answer:**

**NPM Audit:**
```bash
# Check for vulnerabilities
npm audit

# Output example:
# found 2 vulnerabilities (1 moderate, 1 high)

# View detailed report
npm audit --json

# Fix automatically
npm audit fix

# Fix including breaking changes
npm audit fix --force

# Audit production only
npm audit --production
```

**Audit report example:**
```bash
┌───────────────┬──────────────────────────────────────────┐
│ moderate      │ Regular Expression Denial of Service     │
├───────────────┼──────────────────────────────────────────┤
│ Package       │ minimatch                                │
├───────────────┼──────────────────────────────────────────┤
│ Patched in    │ >=3.0.5                                  │
├───────────────┼──────────────────────────────────────────┤
│ Dependency of │ express                                  │
├───────────────┼──────────────────────────────────────────┤
│ Path          │ express > minimatch                      │
└───────────────┴──────────────────────────────────────────┘
```

**Security best practices:**

**1. Keep dependencies updated:**
```bash
# Check outdated packages
npm outdated

# Update
npm update

# Use npm-check-updates
npx npm-check-updates -u
npm install
```

**2. Use exact versions for critical packages:**
```json
{
  "dependencies": {
    "express": "4.18.2",  // Exact, not ^4.18.2
    "jsonwebtoken": "9.0.0"
  }
}
```

**3. Review package before installing:**
```bash
# Check package info
npm view package-name

# Check weekly downloads
npm view package-name downloads

# View repository
npm repo package-name

# Check license
npm view package-name license
```

**4. Use .npmrc for security:**
```bash
# .npmrc
audit=true
audit-level=moderate
```

**5. Enable 2FA:**
```bash
# Enable two-factor auth
npm profile enable-2fa auth-and-writes
```

**6. Use private registry for sensitive packages:**
```bash
# Configure private registry
npm config set registry https://registry.company.com/
```

### Q17: What are NPM best practices?

**Answer:**

**1. Dependency Management:**
```json
{
  // ✅ Use ^ for dependencies
  "dependencies": {
    "express": "^4.18.2"
  },
  
  // ✅ Pin critical versions
  "dependencies": {
    "security-critical": "1.2.3"
  },
  
  // ✅ Separate dev dependencies
  "devDependencies": {
    "jest": "^29.5.0"
  }
}
```

**2. Scripts organization:**
```json
{
  "scripts": {
    // ✅ Clear naming
    "start": "node server.js",
    "dev": "nodemon server.js",
    "build": "webpack --mode production",
    "build:dev": "webpack --mode development",
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "lint": "eslint .",
    "lint:fix": "eslint . --fix"
  }
}
```

**3. Version control:**
```bash
# ✅ Commit package-lock.json
git add package-lock.json

# ✅ Use semantic versioning
npm version patch
npm version minor
npm version major

# ❌ Don't commit node_modules
# Add to .gitignore:
node_modules/
```

**4. Package.json fields:**
```json
{
  "name": "my-package",
  "version": "1.0.0",
  "description": "Clear description",
  "main": "dist/index.js",
  "types": "dist/index.d.ts",  // TypeScript
  "engines": {
    "node": ">=18.0.0",
    "npm": ">=9.0.0"
  },
  "keywords": ["relevant", "keywords"],
  "author": "Name <email@example.com>",
  "license": "MIT",
  "repository": {
    "type": "git",
    "url": "https://github.com/user/repo"
  },
  "bugs": {
    "url": "https://github.com/user/repo/issues"
  },
  "homepage": "https://github.com/user/repo#readme"
}
```

**5. Performance:**
```bash
# ✅ Use npm ci in CI/CD (faster, clean install)
npm ci

# ✅ Cache node_modules in CI
# .github/workflows/ci.yml
- uses: actions/cache@v3
  with:
    path: node_modules
    key: ${{ runner.os }}-node-${{ hashFiles('package-lock.json') }}

# ✅ Remove unused packages
npm prune

# ✅ Analyze bundle size
npx webpack-bundle-analyzer
```

**6. Security:**
```bash
# ✅ Regular audits
npm audit

# ✅ Auto-fix vulnerabilities
npm audit fix

# ✅ Use .npmrc
echo "audit=true" >> .npmrc

# ✅ Enable 2FA
npm profile enable-2fa
```

**7. Publishing:**
```json
{
  // ✅ Specify files to include
  "files": [
    "dist",
    "lib",
    "README.md"
  ],
  
  // ✅ Pre-publish scripts
  "scripts": {
    "prepublishOnly": "npm run test && npm run build"
  }
}
```

---

## Advanced Topics

### Q18: What is NPM workspaces?

**Answer:**
NPM Workspaces allow managing multiple packages in a single repository (monorepo).

**Project structure:**
```
my-monorepo/
├── package.json          # Root package
├── packages/
│   ├── package-a/
│   │   └── package.json
│   ├── package-b/
│   │   └── package.json
│   └── shared/
│       └── package.json
```

**Root package.json:**
```json
{
  "name": "my-monorepo",
  "private": true,
  "workspaces": [
    "packages/*"
  ],
  "scripts": {
    "test": "npm run test --workspaces",
    "build": "npm run build --workspaces"
  },
  "devDependencies": {
    "jest": "^29.5.0"
  }
}
```

**Install dependencies:**
```bash
# Install all workspace dependencies
npm install

# Install for specific workspace
npm install express --workspace=package-a
npm install express -w package-a

# Install in all workspaces
npm install lodash --workspaces
```

**Run scripts:**
```bash
# Run in specific workspace
npm run test --workspace=package-a
npm run build -w package-a

# Run in all workspaces
npm run test --workspaces
npm run test -ws

# Run in workspaces with specific prefix
npm run test --workspaces --if-present
```

**Workspace dependencies:**
```json
// packages/package-b/package.json
{
  "name": "package-b",
  "dependencies": {
    "package-a": "workspace:*",  // Use workspace version
    "express": "^4.18.2"
  }
}
```

**Benefits:**
- Single `node_modules` (shared dependencies)
- Easy to manage related packages
- Cross-package development
- Consistent versioning

### Q19: How do you configure NPM with .npmrc?

**Answer:**
`.npmrc` is the NPM configuration file (per-project, per-user, or global).

**Locations:**
1. **Per-project**: `/path/to/project/.npmrc`
2. **Per-user**: `~/.npmrc`
3. **Global**: `$PREFIX/etc/npmrc`
4. **Built-in**: `/path/to/npm/npmrc`

**Common configurations:**

**Project .npmrc:**
```ini
# Registry
registry=https://registry.npmjs.org/

# Scoped registry
@mycompany:registry=https://registry.company.com/

# Authentication token
//registry.npmjs.org/:_authToken=${NPM_TOKEN}

# Save prefix (^ vs ~)
save-prefix=^

# Save exact versions
save-exact=true

# Engine strict
engine-strict=true

# Audit
audit=true
audit-level=moderate

# Logging
loglevel=warn

# Cache
cache=/path/to/cache

# Package lock
package-lock=true

# Legacy peer deps
legacy-peer-deps=false
```

**Private registry:**
```ini
# .npmrc
registry=https://npm.company.com/
//npm.company.com/:_authToken=${NPM_TOKEN}
```

**Scoped packages:**
```ini
# Public packages from npmjs
registry=https://registry.npmjs.org/

# Company packages from private registry
@company:registry=https://npm.company.com/
```

**Environment variables:**
```bash
# Use in .npmrc
//registry.npmjs.org/:_authToken=${NPM_TOKEN}

# Set in environment
export NPM_TOKEN=your-token-here

# Or in CI/CD
NPM_TOKEN=xxx npm install
```

**View config:**
```bash
# View all config
npm config list

# View specific value
npm config get registry

# Set config
npm config set registry https://registry.npmjs.org/

# Delete config
npm config delete registry
```

### Q20: What is npx and how is it different from npm?

**Answer:**
`npx` is a package runner that comes with NPM 5.2+. It executes packages without installing them globally.

**npm vs npx:**

| Feature | npm | npx |
|---------|-----|-----|
| Purpose | Install packages | Execute packages |
| Installation | Permanent | Temporary |
| Global packages | Required for CLI tools | Not needed |
| Use case | Dependencies | One-time commands |

**Common npx use cases:**

**1. Run without installing:**
```bash
# Instead of:
npm install -g create-react-app
create-react-app my-app

# Use:
npx create-react-app my-app
```

**2. Run different versions:**
```bash
# Run specific version
npx create-react-app@latest my-app
npx webpack@4.0.0

# Test before installing
npx cowsay "Hello!"
```

**3. Run from GitHub:**
```bash
npx github:username/repo
```

**4. Execute local packages:**
```bash
# Without npx
./node_modules/.bin/jest

# With npx
npx jest
```

**5. Run scripts with different Node versions:**
```bash
npx node@14 script.js
npx node@18 script.js
```

**Common npx commands:**
```bash
# Create React app
npx create-react-app my-app

# Create Next.js app
npx create-next-app my-app

# Run ESLint
npx eslint .

# Upgrade packages
npx npm-check-updates -u

# Serve static files
npx http-server

# Kill process on port
npx kill-port 3000

# Bundle analysis
npx webpack-bundle-analyzer

# Generate .gitignore
npx gitignore node
```

**How npx works:**
1. Checks if package exists in local `node_modules/.bin`
2. If not, checks if globally installed
3. If not, downloads temporarily and executes
4. Cleans up temporary packages

**Cache location:**
```bash
# NPX cache (npm 7+)
~/.npm/_npx/
```

---

## Troubleshooting

### Q21: How do you fix common NPM issues?

**Answer:**

**1. Permission errors (EACCES):**
```bash
# ❌ Don't use sudo!

# ✅ Fix: Change npm default directory
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
export PATH=~/.npm-global/bin:$PATH

# Or use nvm (recommended)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
nvm install node
```

**2. Package-lock conflicts:**
```bash
# Delete and regenerate
rm package-lock.json
rm -rf node_modules
npm install

# Or use npm ci (clean install)
npm ci
```

**3. Corrupted cache:**
```bash
# Clear npm cache
npm cache clean --force

# Verify cache
npm cache verify

# Then reinstall
rm -rf node_modules
npm install
```

**4. Dependency conflicts:**
```bash
# See dependency tree
npm ls package-name

# Fix peer dependency issues
npm install --legacy-peer-deps

# Force install (use carefully)
npm install --force
```

**5. Outdated npm/node:**
```bash
# Update npm
npm install -g npm@latest

# Update Node.js (use nvm)
nvm install node
nvm use node
```

**6. Network issues:**
```bash
# Check registry
npm config get registry

# Use different registry
npm config set registry https://registry.npmjs.org/

# Increase timeout
npm config set fetch-timeout 60000

# Use proxy
npm config set proxy http://proxy.company.com:8080
npm config set https-proxy http://proxy.company.com:8080
```

**7. "Cannot find module" errors:**
```bash
# Rebuild native modules
npm rebuild

# Clear and reinstall
rm -rf node_modules package-lock.json
npm install
```

**8. Version conflicts:**
```bash
# Check installed versions
npm list package-name

# Install specific version
npm install package-name@specific-version

# Dedupe dependencies
npm dedupe
```

**9. Slow installations:**
```bash
# Use npm ci (faster in CI)
npm ci

# Disable progress
npm install --no-progress

# Use offline mode
npm install --offline

# Use different registry
npm install --registry https://registry.npm.taobao.org/
```

**10. Package not found:**
```bash
# Clear cache
npm cache clean --force

# Check registry
npm config get registry

# Search package
npm search package-name

# View package info
npm view package-name
```

### Q22: What are NPM alternatives and when to use them?

**Answer:**

**1. Yarn:**
```bash
# Install Yarn
npm install -g yarn

# Commands
yarn install              # npm install
yarn add express          # npm install express
yarn add -D jest          # npm install -D jest
yarn remove lodash        # npm uninstall lodash
yarn upgrade              # npm update

# Benefits:
# - Faster installations (parallel)
# - Deterministic (yarn.lock)
# - Offline mode
# - Workspaces support
```

**2. pnpm:**
```bash
# Install pnpm
npm install -g pnpm

# Commands
pnpm install              # npm install
pnpm add express          # npm install express
pnpm remove lodash        # npm uninstall lodash

# Benefits:
# - Saves disk space (hardlinks)
# - Faster than npm and yarn
# - Strict by default
# - Better monorepo support
```

**3. Bun:**
```bash
# Install Bun
curl -fsSL https://bun.sh/install | bash

# Commands
bun install               # npm install
bun add express           # npm install express
bun remove lodash         # npm uninstall lodash

# Benefits:
# - Extremely fast (10-100x)
# - Built-in bundler
# - TypeScript support
# - Compatible with Node.js
```

**Comparison:**

| Feature | NPM | Yarn | pnpm | Bun |
|---------|-----|------|------|-----|
| Speed | Moderate | Fast | Fast | Fastest |
| Disk Space | More | More | Less | Less |
| Lock File | package-lock.json | yarn.lock | pnpm-lock.yaml | bun.lockb |
| Workspaces | ✅ | ✅ | ✅ | ✅ |
| Offline | ❌ | ✅ | ✅ | ✅ |
| Default | ✅ | ❌ | ❌ | ❌ |

**When to use:**

**NPM:**
- ✅ Default with Node.js
- ✅ Most compatible
- ✅ Simple projects
- ✅ Learning/tutorials

**Yarn:**
- ✅ Need offline mode
- ✅ Workspaces (legacy)
- ✅ Team already uses it

**pnpm:**
- ✅ Monorepos
- ✅ Save disk space
- ✅ Strict dependency management
- ✅ Large projects

**Bun:**
- ✅ Maximum speed
- ✅ Modern projects
- ✅ TypeScript-first
- ✅ Experimental features OK

### Q23: What is the "overrides" field and how does it work?

**Answer:**
The `overrides` field lets you force specific versions for nested dependencies. It's useful for fixing security vulnerabilities or enforcing consistent versions across your project.

**NPM Overrides (NPM 8.3+):**

```json
{
  "name": "my-project",
  "version": "1.0.0",
  "dependencies": {
    "express": "^4.18.2",
    "lodash": "^4.17.21"
  },
  "overrides": {
    "lodash": "4.17.21",  // Force exact version
    "minimatch": "3.1.2",  // Nested dependency override
    "express": {
      "body-parser": "1.20.2"  // Override specific sub-dependency
    }
  }
}
```

**Yarn Resolutions (Yarn equivalent):**

```json
{
  "name": "my-project",
  "version": "1.0.0",
  "dependencies": {
    "express": "^4.18.2",
    "lodash": "^4.17.21"
  },
  "resolutions": {
    "lodash": "4.17.21",
    "minimatch": "3.1.2",
    "express/body-parser": "1.20.2"
  }
}
```

**pnpm Overrides:**

```json
{
  "name": "my-project",
  "version": "1.0.0",
  "dependencies": {
    "express": "^4.18.2",
    "lodash": "^4.17.21"
  },
  "pnpm": {
    "overrides": {
      "lodash": "4.17.21",
      "minimatch": "3.1.2",
      "express>body-parser": "1.20.2"
    }
  }
}
```

**Real-world use cases:**

**1. Security vulnerability in nested dependency:**
```json
{
  "dependencies": {
    "express": "^4.18.2"  // express depends on minimatch with vulnerability
  },
  "overrides": {
    "minimatch": "3.1.2"  // Force patched version
  }
}
```

**2. Conflicting peer dependencies:**
```json
{
  "dependencies": {
    "react": "^18.0.0",
    "next": "^13.0.0"
  },
  "overrides": {
    "react": "18.2.0"  // Ensure all use same React version
  }
}
```

**3. Version standardization across project:**
```json
{
  "overrides": {
    "webpack": "5.88.0",
    "webpack-cli": "5.1.4",
    "lodash": "4.17.21",
    "axios": "1.6.0"
  }
}
```

**Comparison:**

| Feature | NPM Overrides | Yarn Resolutions | pnpm Overrides |
|---------|---------------|------------------|----------------|
| Syntax | JSON path | `/` separator | `>` separator |
| Available | npm 8.3+ | All versions | All versions |
| Nested deps | ✅ Yes | ✅ Yes | ✅ Yes |
| Workspaces | ✅ Yes | ✅ Yes | ✅ Yes |
| Lock file | package-lock.json | yarn.lock | pnpm-lock.yaml |

**How overrides work:**

1. **Resolves to specified version** when package is requested
2. **Applies to all occurrences** in dependency tree
3. **Doesn't change package.json** of dependencies
4. **Recorded in lock file** for reproducibility

**Advanced examples:**

**NPM - Nested override:**
```json
{
  "dependencies": {
    "express": "^4.18.0",
    "jest": "^29.0.0"
  },
  "overrides": {
    "express": {
      "body-parser": "1.20.2",
      "qs": "6.11.0"
    },
    "jest": {
      "jest-circus": "29.5.0"
    }
  }
}
```

**Yarn - Using exact pattern:**
```json
{
  "resolutions": {
    "@babel/core": "7.23.0",
    "@babel/preset-env": "7.23.0",
    "lodash/**/minimatch": "3.1.2"  // Nested pattern
  }
}
```

**pnpm - Using workspace protocol:**
```json
{
  "dependencies": {
    "my-package": "workspace:*"
  },
  "pnpm": {
    "overrides": {
      "lodash": "4.17.21",
      "my-package": "workspace:*"
    }
  }
}
```

**When to use overrides:**

✅ **Use when:**
- Security vulnerability in transitive dependency
- Peer dependency conflicts
- Need consistent versions across large project
- Working with monorepos

❌ **Avoid when:**
- Package maintainer can fix upstream
- Creates security risk by blocking updates
- Incompatible version combinations
- Temporary workaround only

**Troubleshooting:**

**Check what will be overridden:**
```bash
# NPM
npm ls [package-name]

# Yarn
yarn why [package-name]

# pnpm
pnpm why [package-name]
```

**Verify overrides are applied:**
```bash
# Check lock file
grep -A5 "overrides" package-lock.json

# Check installed version
npm list [package-name]
```

**Remove overrides:**
```json
// Simply delete from overrides/resolutions field
{
  "overrides": {
    // "lodash": "4.17.21"  // Removed
  }
}
```

---

## Summary

**Key Takeaways:**

1. **NPM Basics**: Package manager for Node.js, uses package.json and package-lock.json
2. **Dependencies**: Use dependencies for production, devDependencies for development
3. **Versioning**: Follow semantic versioning (MAJOR.MINOR.PATCH)
4. **Scripts**: Automate tasks with npm scripts, use pre/post hooks
5. **Publishing**: Test locally, use .npmignore, publish with `npm publish`
6. **Security**: Run `npm audit`, keep dependencies updated, enable 2FA
7. **Workspaces**: Manage monorepos efficiently
8. **Configuration**: Use .npmrc for project/user configs
9. **npx**: Execute packages without global installation
10. **Alternatives**: Consider Yarn, pnpm, or Bun for specific needs

**Best Practices:**
- ✅ Commit package-lock.json
- ✅ Use semantic versioning
- ✅ Separate dev and production dependencies
- ✅ Run security audits regularly
- ✅ Use npm ci in CI/CD
- ✅ Keep dependencies updated
- ✅ Use .npmrc for team consistency
- ✅ Document scripts in README
- ❌ Never commit node_modules
- ❌ Don't use sudo with npm
