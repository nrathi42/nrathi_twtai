---
name: confluence-to-user-guide
description: Transform technical Confluence documentation into clean, user-friendly end-user guides. Use this skill whenever you need to convert internal Confluence pages (technical specifications, API docs, release notes, architecture docs, dev guides, etc.) into polished HTML guides for non-technical business users. Automatically filters internal jargon, removes developer notes and sensitive infrastructure data, verifies Microsoft technology claims against official documentation, and restructures content for clarity. Accepts Confluence URLs or raw page content. Outputs formatted, accessible HTML suitable for external distribution or internal business audiences.
compatibility: |
  - Requires ability to fetch web content (for Confluence URLs)
  - Requires access to Microsoft documentation for tech verification
  - Python/HTML generation capability
---

## Overview

This skill transforms technical Confluence documentation into user-friendly guides using the **RTCCO format**:
- **Role**: You are a senior technical editor specializing in creating end user guide
- **Task**: Review this Confluence page endpoints for completeness and style
- **Context**: "Our audience is junior developers. We follow the Microsoft manual Style Guide."
- **Constraints**: Verify accuracy (especially Microsoft claims) and filter sensitive data like links, todo
- **Output**: Generate polished HTML guide

## Input Handling

### Accept Both Formats

The skill should gracefully handle:

1. **Confluence URLs** (preferred for automatic extraction)
   - Pattern: `https://*.atlassian.net/wiki/spaces/*/pages/*`
   - Extracts content automatically via web fetch
   - Preserves page hierarchy and metadata

2. **Raw Content** (for pre-extracted pages)
   - Accepts markdown, plain text, or HTML copied from Confluence
   - Handles pasted content directly
   - User specifies title and context

### Handling Multiple Pages (Up to 100 limit)
If processing a folder/collection:
- Accept comma-separated URLs or a list of page IDs
- Process each page independently
- Create a table of contents linking all guides
- Generate a single index HTML with navigation

---

## Phase 1: READ - Content Extraction & Analysis

### Extract Content Intelligently

When given a Confluence URL or raw content:

1. **Identify content type**:
   - Release notes, guides, API documentation, architecture docs, process docs, etc.
   - Adjust processing strategy based on type

2. **Extract page structure**:
   - Headings and hierarchy
   - Sections and subsections
   - Lists (bulleted and numbered)
   - Tables and diagrams
   - Code blocks and examples
   - Links and references

3. **Capture metadata**:
   - Original page title
   - Last modified date
   - Page summary/description
   - Related pages or links

---

## Phase 2: THINK - Analysis for Improvement

### Analyze for Clarity Issues

Identify sections that need restructuring:
- Dense paragraphs (>150 words) → break into logical chunks
- Technical jargon → list for replacement
- Unclear purpose statements → mark for rewriting
- Missing context or prerequisites
- Orphaned information (appears without introduction)

### Map Content to User Needs

For non-technical business users:
- What problem does this solve?
- What action should users take?
- What outcomes can they expect?
- What are the prerequisites?
- When would they use this?

---

## Phase 3: CREATE - Rewrite for Clarity

### Language Guidelines

**Replace technical jargon** with business equivalents:
- ❌ "API endpoint" → ✅ "service connection point"
- ❌ "payload" → ✅ "data"
- ❌ "microservices architecture" → ✅ "modular system"
- ❌ "SSL/TLS certificate" → ✅ "security certificate"
- ❌ "latency" → ✅ "response time"
- ❌ "deployment" → ✅ "release" or "rollout"

**Restructure for clarity**:
- Start with **Why**: Why is this feature/guide important?
- Then **What**: What exactly does it do?
- Then **How**: How do users access or use it?
- Then **When**: When should they use it?
- Finally **Help**: Where can they get support?

### Content Reorganization

Reorder sections logically:

```
1. Overview / Introduction
   - Clear description of what this is
   - Who should use it and when
   - Key benefits or outcomes

2. Prerequisites / Getting Started
   - What users need before they start
   - Account requirements, permissions, etc.

3. Step-by-Step Instructions (if applicable)
   - Each step numbered and focused
   - One action per step
   - Include "What you'll see" for each step

4. Common Scenarios / Examples
   - Real-world use cases
   - Screenshots or examples (if available)

5. Frequently Asked Questions
   - Common confusion points
   - Troubleshooting

6. Get Help
   - Contact information
   - Support resources
   - Additional learning
```

### Formatting Best Practices

- **Headings**: Use clear, descriptive headings (not just "Overview")
  - ✅ "How to set up your account in 5 steps"
  - ❌ "Setup"
- **Lists**: Use bullets for options, numbers for steps
- **Emphasis**: Bold key terms users will search for; italic for definitions
- **Examples**: Include specific examples wherever possible
- **Tables**: For comparisons or feature matrices (simple, not complex)
- **Callout boxes**: For tips, warnings, or important notes

---

## Phase 4: CHECK - Filtering & Verification

### Filter Sensitive Data

**Remove immediately** (do not include):

1. **Infrastructure identifiers**:
   - IP addresses (both public and private: 192.168.x.x, 10.0.x.x, 172.16.x.x)
   - Hostnames and domain names (internal servers, e.g., `api-internal.company.local`)
   - API endpoints and URLs (internal or staging: `staging-api.internal`, `dev-config-server`)
   - Container/cluster names
   - Database names or connection strings
   - SSH keys, secrets, credentials (any form)

2. **Internal notes and comments**:
   - Anything marked `TODO:`, `FIXME:`, `HACK:`, `XXX:`
   - Comments like "Internal note:", "Dev team only:", "Internal discussions:"
   - Metadata about internal processes or delays
   - Decision rationales (if internal-facing)

3. **Employee references**:
   - Names linked to mistakes or internal discussions
   - Internal contacts (replace with generic "Contact support")
   - Team-specific information

4. **Proprietary information**:
   - Performance metrics (unless meant for users)
   - Internal tools and systems
   - Architecture diagrams showing internal systems
   - Code snippets showing internal patterns

### Verify Microsoft Technology Claims

**For any mention of Microsoft products/services**:

1. **Identify all Microsoft references**:
   - Product names (Azure, Office 365, Windows, etc.)
   - Feature names
   - Version numbers
   - Pricing claims
   - Compatibility statements

2. **Cross-check with official docs**:
   - Use Microsoft Learn (https://learn.microsoft.com/)
   - Use Microsoft Docs (https://docs.microsoft.com/)
   - Use Azure documentation (https://azure.microsoft.com/en-us/documentation/)
   - Verify:
     - ✓ Product name is current (not deprecated)
     - ✓ Feature exists and works as described
     - ✓ Version/pricing info is current
     - ✓ System requirements are accurate
     - ✓ No mentions of deprecated features

3. **Update if outdated**:
   - Replace deprecated product names with current equivalents
   - Flag if feature has changed significantly
   - Note if there are newer alternatives
   - Add version clarity (e.g., "as of 2026")

4. **Handle uncertain claims**:
   - If a claim can't be verified, either remove it or mark it clearly as "Please verify with your IT team"
   - Flag for user review: "⚠️ This claim requires verification with current Microsoft documentation"

### Example Filtering

**Before:**
```
Internal API Endpoint: https://config-api.internal:8443/v2/settings
TODO: Update this for Azure migration (see ticket INFRA-4521)
Patch by John in DevOps team - internal decision to use mTLS certificates
Requires: Windows Server 2019 (ask James in ops)
Note: SSO integration partially broken, waiting for Auth team
```

**After:**
```
Configuration Management Service
Requires: Windows Server 2019 or later
Setup includes: Secure authentication and role-based access
Note: If you need single sign-on integration, contact your IT support team
```

---

## Phase 5: OUTPUT - HTML Generation

### HTML Structure

Generate a **semantic, accessible HTML** document with:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[Page Title]</title>
  <style>
    /* Include embedded CSS for standalone HTML */
  </style>
</head>
<body>
  <header>
    <h1>[Title]</h1>
    <p class="summary">[One-sentence summary]</p>
    <p class="meta">Last updated: [Date]</p>
  </header>
  
  <nav class="toc">
    <!-- Table of contents if page is long -->
  </nav>
  
  <main>
    <!-- Content sections -->
  </main>
  
  <footer>
    <p>Need help? Contact [Support Info]</p>
    <p>Last reviewed: [Date]</p>
  </footer>
</body>
</html>
```

### CSS Styling Guidelines

- **Font**: Sans-serif for body (Segoe UI, Arial, or system default), readable size (16px minimum)
- **Colors**: High contrast for accessibility (WCAG AA standard minimum)
- **Headings**: Clear hierarchy with appropriate sizing
- **Line length**: 60-80 characters for readability
- **Spacing**: Generous whitespace between sections
- **Mobile-friendly**: Responsive design that works on phones/tablets
- **Print-friendly**: Styles that work well when printed

### Content Callouts

Use distinctive styling for:

```html
<div class="note">
  💡 <strong>Tip:</strong> [Helpful information]
</div>

<div class="warning">
  ⚠️ <strong>Important:</strong> [Critical information users must know]
</div>

<div class="success">
  ✓ <strong>Good to know:</strong> [Positive outcome or best practice]
</div>

<div class="example">
  📋 <strong>Example:</strong> [Real-world scenario]
</div>
```

### Multi-Page Output

For multiple pages (folder/collection):

1. **Create index.html** with:
   - Overview of all guides
   - Quick navigation
   - Search functionality (if possible)
   - Last update timestamps

2. **Create individual guide pages**: Each as separate HTML file

3. **Link structure**:
   - Each guide links back to index
   - Related guides linked at bottom
   - Breadcrumb navigation for context

---

## Workflow Summary

When you receive a request to transform Confluence content:

1. **Clarify input**: Ask if URL or raw content
2. **Extract** (READ): Get all content and metadata
3. **Analyze** (THINK): Identify structure, jargon, unclear sections
4. **Rewrite** (CREATE): Apply clarity guidelines, restructure for users
5. **Filter & Verify** (CHECK): Remove sensitive data, verify Microsoft claims
6. **Generate** (OUTPUT): Create clean, accessible HTML
7. **Review**: Confirm with user before finalizing

---

## Example Transformation

### Input (Raw Confluence):
```
# Project Configuration Service v2

The PCS v2 provides centralized mgmt of config artifacts across all 
microservices. Uses gRPC protocol for low-latency inter-service communication.

TODO: Update docs for Azure migration
Deployment: SSH to config-api.internal (10.0.42.5) and run deploy script

### Prerequisites
- Linux knowledge required
- Kubernetes cluster access

### Integration Points
See internal wiki for mTLS setup (needs IT approval from James)
```

### Output (User-Friendly HTML):
```html
<h1>Managing Application Settings</h1>

<div class="summary">
  <p>The Settings Management Service lets you update application 
  configurations from one central location without restarting services.</p>
</div>

<h2>When You'd Use This</h2>
<ul>
  <li>Updating feature flags or feature switches</li>
  <li>Changing application behavior without releasing new code</li>
  <li>Managing environment-specific settings</li>
</ul>

<h2>Getting Started</h2>
<ol>
  <li>Request access from your IT support team</li>
  <li>Log in with your company credentials</li>
  <li>Navigate to your application's settings</li>
</ol>

<div class="note">
  💡 <strong>Tip:</strong> Changes take effect immediately—no restart needed.
</div>

<h2>Need Help?</h2>
<p>Contact your IT support team or email support@company.com</p>
```

---

## Tips for Success

- **Always ask**: Confirm page type and primary audience with user
- **Show your work**: Highlight what you filtered out and why
- **Test readability**: Could a non-technical person understand this?
- **Check all links**: Ensure references still work after filtering
- **Maintain accuracy**: Don't oversimplify technical concepts; clarify them
- **Use real examples**: Abstract explanations confuse users
- **Be consistent**: Same terms throughout, consistent formatting
- **Accessibility matters**: Use semantic HTML, alt text for images, proper heading hierarchy
- **Push Output to Confluence Page**: Confirm page where to push output
