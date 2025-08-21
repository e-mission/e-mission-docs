# e-mission Documentation Repository

**ALWAYS FOLLOW THESE INSTRUCTIONS FIRST.** Only search for additional information or use alternative approaches if the information here is incomplete or you encounter unexpected errors.

e-mission is an open-source mobility platform with a mobile app and server backend. This repository contains comprehensive documentation built with MkDocs for the entire e-mission ecosystem.

## Working Effectively

### Bootstrap and Build Documentation
Install dependencies and build the documentation:

```bash
# Install Python dependencies
python3 -m pip install --user -r requirements.txt

# Build documentation - FAST: takes ~2 seconds 
python3 -m mkdocs build

# Serve locally for development - starts in ~2 seconds
python3 -m mkdocs serve --dev-addr=0.0.0.0:8000
```

**TIMING:** Build takes 1-2 seconds. Server starts immediately. No long timeouts needed.

### Validation

After making documentation changes:

1. **ALWAYS build and verify no new errors:**
   ```bash
   python3 -m mkdocs build
   ```

2. **Test locally by serving and checking your changes:**
   ```bash
   python3 -m mkdocs serve --dev-addr=0.0.0.0:8000
   ```
   Navigate to `http://localhost:8000` to view documentation.

3. **Check for broken links and missing references in build output** - MkDocs will warn about:
   - Files not in navigation (`mkdocs.yml`)
   - Broken internal links
   - Missing target files

### Documentation Structure

Follow the established directory structure:
- `docs/` - All documentation files (Markdown)
- `assets/` - Images and media files (use relative paths)
- `mkdocs.yml` - Site configuration and navigation
- `requirements.txt` - Python dependencies (mkdocs, pygments, pymdown-extensions)

### Adding New Documentation

1. Create Markdown files in appropriate `docs/` subdirectory
2. Add images to corresponding `assets/` subdirectory  
3. Update `mkdocs.yml` navigation if the page should be in the main menu
4. Use relative paths for images: `../../assets/section/image.png`
5. Build and test locally before committing

## e-mission Ecosystem Context

This documentation repository covers the entire e-mission platform. The e-mission platform consists of multiple repositories:

### Core Repositories
- **e-mission-docs** (this repo): Comprehensive documentation
- **e-mission-server**: Python backend - refer to that repository for server setup and development
- **e-mission-phone**: Cordova mobile app - refer to that repository for mobile development  
- **e-mission-devapp**: Development app for testing
- **e-mission-docker**: Docker deployment configurations

**This repository is documentation-only** - no server setup or mobile app development is required here.

## Documentation Workflow

### For Documentation Changes (This Repository Only)
```bash
# 1. Make changes to Markdown files in docs/
# 2. Build and test
python3 -m mkdocs build
python3 -m mkdocs serve --dev-addr=0.0.0.0:8000
# 3. Commit changes (site/ directory is automatically ignored)
```

**For server setup, mobile development, or platform development**, refer to the respective repositories listed above rather than this documentation repository.

## Key Documentation Sections

### Quick Reference - Important Pages
- `docs/dev/tutorial/small_ui_changes/quickstart.md` - 7-step development environment setup
- `docs/install/manual_install.md` - Server installation guide  
- `docs/dev/archi/module_structure.md` - Architecture overview
- `docs/dev/front/high_level_faq.md` - Common development issues
- `docs/contribute_to_the_doc/CONTRIBUTING.md` - Documentation contribution guide

### Documentation Development Workflow
- **Documentation Changes**: Edit markdown files in `docs/`, build with MkDocs, test locally
- **Asset Management**: Add images to `assets/` directory with relative paths
- **Navigation**: Update `mkdocs.yml` for new pages

## Repository-Specific Details

### Files That Should NOT Be Committed
- `site/` directory (build output) - automatically ignored in `.gitignore`
- Python cache files (`__pycache__/`, `*.pyc`)
- Editor temporary files

### Navigation Management
Edit `mkdocs.yml` to:
- Add new pages to navigation menu
- Reorganize documentation structure  
- Configure site metadata and theme options

### Asset Management
- Store images in `assets/` with same directory structure as `docs/`
- Use relative paths: `![alt text](../../assets/section/image.png)`
- Keep images organized by documentation section

## Troubleshooting

### Build Issues
- **Missing dependencies**: Run `pip install -r requirements.txt`
- **Permission errors**: Use `--user` flag with pip
- **Import errors**: Ensure Python 3 is used (`python3`)

### Common MkDocs Warnings
- Files not in nav: Add to `mkdocs.yml` or ignore if intentional
- Broken links: Check file paths and ensure targets exist
- Missing anchors: Verify internal page links are correct

### Performance
- Build time: 1-2 seconds (very fast)
- No need for long timeouts or build optimization
- Serve starts immediately for live preview

Remember: This is a **documentation-only repository**. For issues with the actual e-mission server or mobile app, refer to their respective repositories and documentation sections.