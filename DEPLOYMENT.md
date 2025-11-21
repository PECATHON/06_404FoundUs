# Deployment Guide

This guide will walk you through deploying the Travel Assistant application to production using Railway (backend) and Vercel (frontend).

## Prerequisites

- GitHub account with your code pushed
- Railway account (https://railway.app)
- Vercel account (https://vercel.com)
- Google API Key for Gemini

---

## Part 1: Deploy Backend to Railway

### Step 1: Create Railway Project

1. Go to https://railway.app and sign in
2. Click **"New Project"**
3. Select **"Deploy from GitHub repo"**
4. Choose your repository: `PECATHON/06_404FoundUs`
5. Railway will detect your project

### Step 2: Configure Backend Service

1. Railway should auto-detect the backend. If not:
   - Click **"Add Service"** → **"GitHub Repo"**
   - Select the `backend` directory as the root

2. **Set Environment Variables:**
   - Click on your service → **"Variables"** tab
   - Add the following:
     ```
     GOOGLE_API_KEY=<your-gemini-api-key>
     PORT=8000
     PYTHONUNBUFFERED=1
     ```

3. **Configure Build Settings:**
   - Railway should auto-detect `Procfile` and `runtime.txt`
   - If not, go to **Settings** → **Build**:
     - Build Command: `pip install -r requirements.txt`
     - Start Command: `uvicorn app.main:app --host 0.0.0.0 --port $PORT`

### Step 3: Deploy Backend

1. Click **"Deploy"** (Railway will start building)
2. Wait for deployment to complete (check logs for any errors)
3. Once deployed, Railway will provide a URL like:
   ```
   https://your-backend.up.railway.app
   ```
4. **Copy this URL** - you'll need it for the frontend!

### Step 4: Test Backend

1. Open the Railway URL in your browser
2. You should see: `{"message": "Travel Assistant API is running"}`
3. Test WebSocket by visiting: `https://your-backend.up.railway.app/docs`

---

## Part 2: Deploy Frontend to Vercel

### Step 1: Create Vercel Project

1. Go to https://vercel.com and sign in
2. Click **"Add New"** → **"Project"**
3. Import your GitHub repository: `PECATHON/06_404FoundUs`
4. Vercel will detect it's a React app

### Step 2: Configure Frontend Build

1. **Framework Preset:** Vite (should auto-detect)
2. **Root Directory:** `frontend`
3. **Build Command:** `npm run build`
4. **Output Directory:** `dist`
5. **Install Command:** `npm install`

### Step 3: Set Environment Variables

1. In Vercel project settings → **"Environment Variables"**
2. Add the following variable:
   ```
   VITE_API_URL=https://your-backend.up.railway.app
   ```
   ⚠️ **IMPORTANT:** Replace `your-backend.up.railway.app` with your actual Railway backend URL from Part 1, Step 3!

### Step 4: Deploy Frontend

1. Click **"Deploy"**
2. Vercel will build and deploy your frontend
3. Once complete, you'll get a URL like:
   ```
   https://your-app.vercel.app
   ```

---

## Part 3: Connect Frontend to Backend

### Update CORS Settings (if needed)

If you encounter CORS errors:

1. Go to Railway → Your Backend Service → **Variables**
2. Add:
   ```
   ALLOWED_ORIGINS=https://your-app.vercel.app
   ```
3. Redeploy the backend

The backend already has CORS configured in `backend/app/main.py`, but you may need to add your Vercel domain explicitly.

---

## Part 4: Verify Deployment

### Test the Complete Flow

1. Open your Vercel URL: `https://your-app.vercel.app`
2. Try the following:
   - ✅ Voice input (click microphone icon)
   - ✅ Search for flights: "Flights to JFK with stops"
   - ✅ Verify multi-leg flights show with layover info
   - ✅ Select a flight
   - ✅ Search for hotels: "Hotels in Tokyo"
   - ✅ Switch back to flights: "Just book the flight"
   - ✅ Verify hotel results disappear and flight is remembered

### Check Logs

**Railway (Backend):**
- Go to your service → **"Deployments"** → Click latest deployment
- View logs to debug any issues

**Vercel (Frontend):**
- Go to your project → **"Deployments"** → Click latest deployment
- View **"Functions"** logs for any errors

---

## Environment Variables Summary

### Backend (Railway)
```
GOOGLE_API_KEY=<your-gemini-api-key>
PORT=8000
PYTHONUNBUFFERED=1
ALLOWED_ORIGINS=https://your-app.vercel.app  # Optional, if CORS issues
```

### Frontend (Vercel)
```
VITE_API_URL=https://your-backend.up.railway.app
```

---

## Troubleshooting

### Issue: "WebSocket connection failed"
**Solution:** 
- Verify `VITE_API_URL` in Vercel is set correctly
- Check Railway backend is running (green status)
- Ensure Railway URL uses `https://` (not `http://`)

### Issue: "CORS error"
**Solution:**
- Add your Vercel domain to `ALLOWED_ORIGINS` in Railway
- Redeploy backend after adding the variable

### Issue: "Transcription not working"
**Solution:**
- Verify `GOOGLE_API_KEY` is set in Railway
- Check Railway logs for API errors
- Ensure your API key has Gemini API access enabled

### Issue: "Build failed on Railway"
**Solution:**
- Check `requirements.txt` has all dependencies
- Verify `runtime.txt` specifies Python 3.9+
- Check Railway build logs for specific errors

### Issue: "Build failed on Vercel"
**Solution:**
- Verify `frontend` is set as root directory
- Check `package.json` has correct build scripts
- Ensure `VITE_API_URL` is set before build

---

## Post-Deployment

### Custom Domain (Optional)

**Vercel:**
1. Go to Project Settings → **"Domains"**
2. Add your custom domain
3. Follow DNS configuration instructions

**Railway:**
1. Go to Service Settings → **"Domains"**
2. Add custom domain
3. Update DNS records as instructed

### Monitoring

- **Railway:** Built-in metrics and logs
- **Vercel:** Analytics and Web Vitals available in dashboard

---

## Quick Reference

| Service | Platform | URL Pattern | Purpose |
|---------|----------|-------------|---------|
| Backend | Railway | `https://*.up.railway.app` | API + WebSocket |
| Frontend | Vercel | `https://*.vercel.app` | React UI |

**Need Help?**
- Railway Docs: https://docs.railway.app
- Vercel Docs: https://vercel.com/docs
- Check application logs in respective platforms
