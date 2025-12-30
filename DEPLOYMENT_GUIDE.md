# Deployment Guide for Doctor Website

## Pre-Deployment Checklist

### 1. Compile SCSS for Production
Before uploading to server, compile your SCSS with compression:

```bash
cd /Users/jagatpatel/Desktop/doctor
sass assets/scss/main.scss assets/css/custom.css --style=compressed
```

This creates a minified CSS file for faster loading.

---

## Deployment Options

### Option 1: Static File Hosting (Recommended)
**Best for:** Shared hosting, cPanel, Apache, Nginx

#### Files to Upload:
Upload the entire `doctor` folder to your server, including:
- ✅ All HTML files (*.html)
- ✅ `assets/` folder (with compiled CSS)
- ✅ `admin/` folder (if needed)
- ✅ `pharmacy/` folder (if needed)
- ❌ `assets/scss/` folder (optional - not needed on server)

#### Upload Methods:
1. **FTP/SFTP** (FileZilla, Cyberduck)
2. **cPanel File Manager**
3. **rsync** (command line)

#### Example rsync command:
```bash
rsync -avz --exclude 'assets/scss' --exclude '.DS_Store' \
  /Users/jagatpatel/Desktop/doctor/ \
  username@your-server.com:/path/to/public_html/
```

---

### Option 2: Deploy with SCSS Compilation on Server
**Best for:** VPS, Cloud servers with SSH access

#### Requirements on Server:
- Node.js or Dart Sass installed
- SSH access

#### Steps:

1. **Upload all files including SCSS:**
```bash
rsync -avz /Users/jagatpatel/Desktop/doctor/ \
  username@your-server.com:/path/to/website/
```

2. **SSH into your server:**
```bash
ssh username@your-server.com
```

3. **Install Sass on server:**
```bash
# Option A: Using npm (if Node.js is installed)
npm install -g sass

# Option B: Using standalone Dart Sass
wget https://github.com/sass/dart-sass/releases/download/1.69.5/dart-sass-1.69.5-linux-x64.tar.gz
tar -xzf dart-sass-1.69.5-linux-x64.tar.gz
sudo mv dart-sass/sass /usr/local/bin/
```

4. **Navigate to your website directory:**
```bash
cd /path/to/website
```

5. **Compile SCSS:**
```bash
sass assets/scss/main.scss assets/css/custom.css --style=compressed
```

6. **Optional - Set up auto-compilation:**
Create a script to watch for changes:
```bash
#!/bin/bash
sass --watch assets/scss/main.scss:assets/css/custom.css --style=compressed
```

---

## Quick Upload Commands

### Using FTP (with lftp):
```bash
lftp -u username,password ftp.your-server.com
cd public_html
mirror -R /Users/jagatpatel/Desktop/doctor ./
bye
```

### Using SCP:
```bash
scp -r /Users/jagatpatel/Desktop/doctor/* \
  username@your-server.com:/path/to/public_html/
```

### Using rsync (recommended):
```bash
# Exclude unnecessary files
rsync -avz --progress \
  --exclude 'assets/scss' \
  --exclude '.DS_Store' \
  --exclude '.git' \
  --exclude 'node_modules' \
  /Users/jagatpatel/Desktop/doctor/ \
  username@your-server.com:/path/to/public_html/
```

---

## After Deployment

### 1. Test Your Site
Visit your website URL and check:
- ✅ Styles are loading correctly
- ✅ Images are displaying
- ✅ Links are working
- ✅ Primary color is red (as configured)

### 2. Browser Cache
If styles don't update, clear browser cache or do a hard refresh:
- **Chrome/Firefox:** Ctrl+Shift+R (Windows) or Cmd+Shift+R (Mac)
- **Safari:** Cmd+Option+R

### 3. File Permissions
Ensure proper permissions on server:
```bash
# For files
find /path/to/public_html -type f -exec chmod 644 {} \;

# For directories
find /path/to/public_html -type d -exec chmod 755 {} \;
```

---

## Making Changes After Deployment

### If you deployed WITHOUT SCSS on server:
1. Make changes to SCSS files locally
2. Compile: `sass assets/scss/main.scss assets/css/custom.css --style=compressed`
3. Upload only the `assets/css/custom.css` file to server

### If you deployed WITH SCSS on server:
1. Make changes to SCSS files locally
2. Upload changed SCSS files
3. SSH into server and run: `sass assets/scss/main.scss assets/css/custom.css --style=compressed`

---

## Troubleshooting

### Styles not loading?
1. Check browser console for 404 errors
2. Verify CSS file path in HTML matches server structure
3. Clear browser cache
4. Check file permissions (should be 644 for files)

### Images not showing?
1. Verify image paths are relative (not absolute)
2. Check file permissions
3. Ensure images were uploaded

### Need to change primary color on server?
1. Edit `assets/scss/utils/variables.scss` (line 20)
2. Compile SCSS: `sass assets/scss/main.scss assets/css/custom.css --style=compressed`
3. Upload the new `custom.css` to server

---

## Recommended Server Structure

```
public_html/
├── index.html
├── about-us.html
├── (all other .html files)
├── assets/
│   ├── css/
│   │   ├── custom.css (compiled, minified)
│   │   ├── bootstrap.min.css
│   │   └── (other CSS files)
│   ├── js/
│   ├── img/
│   ├── fonts/
│   └── plugins/
├── admin/
└── pharmacy/
```

**Note:** The `assets/scss/` folder is optional on the server if you compile locally.

---

## Production Optimization Tips

1. **Minify CSS:** Already done with `--style=compressed`
2. **Enable Gzip:** Configure on server (Apache/Nginx)
3. **Browser Caching:** Add cache headers in `.htaccess` or server config
4. **CDN:** Consider using CDN for static assets

---

## Need Help?

If you encounter issues:
1. Check server error logs
2. Verify file permissions
3. Test locally first
4. Contact your hosting provider for server-specific configuration
