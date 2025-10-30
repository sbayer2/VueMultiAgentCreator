# Project Changelog

## Cloud SQL Security Enhancement - FULLY SECURED 🔒 (2025-10-30)

**Security Issue Resolved**: Cloud SQL instance was publicly accessible with `0.0.0.0/0` authorization, exposing database to potential brute force attacks from the internet.

**Root Cause**: Legacy TCP connection configuration allowed any IP address to attempt database connections. While credentials were still required, this created an unnecessary attack surface.

**Security Implementation Applied**:
1. **Enabled Cloud SQL Admin API** for the project
2. **Implemented Cloud SQL Auth Proxy** - Secure connection method for Cloud Run
3. **Updated database connection logic** (`backend/models/database.py:118-134`)
   - Auto-detects `INSTANCE_CONNECTION_NAME` environment variable
   - Uses Unix socket path `/cloudsql/mythic-aloe-467602-t4:us-central1:vue-multiagent-db`
   - Falls back to TCP for local development
4. **Updated deployment configuration** (`cloudbuild.yaml:31-34`)
   - Added `--add-cloudsql-instances` flag
   - Added `INSTANCE_CONNECTION_NAME` environment variable
   - Removed `DB_HOST` (no longer needed for production)
5. **Removed public IP authorization** - Cleared all authorized networks from Cloud SQL instance

**Security Improvements**:
- ✅ **No Brute Force Risk**: Database not accessible from public internet
- ✅ **No Port Scanning**: Database port not exposed to attackers
- ✅ **Encrypted Connection**: Cloud SQL Auth Proxy uses secure IAM-authenticated channels
- ✅ **Zero Configuration**: No IP whitelisting needed for Cloud Run
- ✅ **Automatic IAM**: Uses Google Cloud IAM for authentication

**Technical Details**:
- Connection Method: Unix socket via Cloud SQL Auth Proxy (production) / TCP (development)
- Files Modified:
  - `backend/models/database.py` - Added Unix socket detection logic
  - `cloudbuild.yaml` - Added Cloud SQL instance connection configuration
- Cloud SQL Configuration: Authorized networks cleared (empty list)
- Instance Connection Name: `mythic-aloe-467602-t4:us-central1:vue-multiagent-db`

**Testing Results**:
- ✅ Health check: Healthy
- ✅ Database connection: Connected via Unix socket (`db_host: null`)
- ✅ Test query: Success
- ✅ API endpoints: 200 OK
- ✅ All app functionality: Working normally

**Before → After**:
```
BEFORE (INSECURE) 🔴
✗ Public IP: Enabled with 0.0.0.0/0 authorization
✗ Connection: TCP over public internet
✗ Attack Surface: HIGH

AFTER (SECURE) ✅
✅ Public IP: Enabled but NO authorized networks
✅ Connection: Unix socket via Cloud SQL Auth Proxy
✅ Attack Surface: MINIMAL
```

---

## Update Assistant Functionality - FULLY WORKING ✅ (2025-09-18)

**Issue Resolved**: The "Update Assistant" button was crashing with validation errors and not properly updating existing assistants.

**Root Cause**: Frontend form validation failure due to improper form data population in `AssistantEditView.vue`. The `useForm` composable requires `setFieldValue()` method instead of direct assignment to reactive form fields.

**Fix Applied**:
1. **Backend was already correct**: Using `client.beta.assistants.update(assistant_id, **update_data)` - the proper OpenAI Assistants API modify endpoint that preserves the same `assistant_id`
2. **Frontend form fix**: Replaced direct assignments `form.value.name = data.name` with proper `setFieldValue('name', data.name)` calls
3. **Validation flow**: Form now properly populates with assistant data, passes validation, and submits successfully

**Technical Details**:
- File: `frontend/src/views/AssistantEditView.vue`
- Fix: Added `setFieldValue` to useForm destructuring and used it for form population
- Backend: `backend/api/assistants.py` line 270 - correct OpenAI modify endpoint usage
- Result: Complete end-to-end Update Assistant workflow functional

**Testing Results**:
- ✅ Form loads with assistant data properly populated
- ✅ Validation passes (no more "name required" errors)
- ✅ Backend calls OpenAI's modify assistant endpoint (preserving assistant_id)
- ✅ Assistant gets updated with new field values
- ✅ User redirected back to assistants list

---

## Delete Functionality Fixed and Optimized (2025-09)

**Backend Fixes**:
1. Fixed the 500 error with tool_resources update when deleting files
2. Proper empty file handling - explicitly passes empty array when no files remain
3. Better error handling for files that don't exist in OpenAI

**Frontend Optimizations**:
1. Optimistic UI updates - files immediately removed from UI
2. Proper event flow - delete button → local function → optimistic update → emit to parent
3. Error feedback logging

**Complete Flow**:
1. User clicks delete → Confirmation dialog
2. UI immediately removes the file (optimistic update)
3. Backend removes file from assistant's tool_resources in OpenAI
4. File deleted from OpenAI storage
5. Database metadata cleaned up

---

## Large Response Handling (2025-09)

**Issue**: "Unable to connect to the server" error with large formatted responses (4K+ characters with legal citations)

**Fix Applied**:
- Enhanced nginx buffer configuration
- Multi-part response handling
- Proper message aggregation from OpenAI
- Extended timeouts for complex assistant processing

**Result**: Legal assistant now handles complex Bluebook citation responses smoothly

---

## Authentication Flow - FULLY WORKING ✅

**Current State**:
- JWT tokens stored in localStorage as 'auth_token'
- Axios interceptors configured in setupInterceptors.ts
- Auth state managed by Pinia store with persistence
- Token validation prevents storing invalid tokens
- API response parsing handles backend's nested format

**Known Issues Resolved**:
- ✅ CORS: Fixed by updating FRONTEND_URL to new deployment URL
- ✅ Auth redirect: Fixed API response parsing and token validation
- ✅ Token attachment: Fixed by proper axios interceptor setup
- ✅ API paths: Frontend correctly uses /api prefix

---

## Forgot Password Feature (2025-09-22)

**Implemented**:
- Email sending via Gmail SMTP with App Password
- Secure JWT reset tokens with 1-hour expiry
- HTML email templates
- Password update functionality

**Configuration**:
- Gmail SMTP with App Password authentication
- Email templates with reset button/link
- Secure token generation and validation

---

## Assistant-Specific Threads (Option 2 Implementation)

**Architecture**:
1. Database: Added thread_id column to user_assistants table
2. Backend: Updated endpoints to use assistant-specific threads
3. Thread Management: Each user-assistant pair gets persistent OpenAI thread
4. New Thread API: Users can reset threads for specific assistants

**Benefits**:
- ✅ Multiple concurrent conversations with different assistants
- ✅ Thread persistence - conversations resume where left off
- ✅ Thread reset capability per assistant
- ✅ Proper MMACTEMP pattern: create → messages.create → runs.create flow
- ✅ Immediate chat interface activation

---

## MMACTEMP Pattern Implementation

**File Purpose Assignment**:
- Image files (.jpg, .jpeg, .png, .webp, .gif) → purpose: 'vision' ✅
- Document files → purpose: 'assistants' ✅
- Frontend preference honored if explicitly sent

**Chat Processing**:
- Vision files → Added to message content for immediate viewing
- Assistant files → Added to tool_resources for code_interpreter
- Proper separation between file types

**Result**: Clean separation of vision and assistant files like cloud-deployed MMACTEMP app

---

## Debugging Practices

### Common Authentication Issues (Now Resolved)
1. **Token not attached**: Check `utils/setupInterceptors.ts` axios configuration
2. **CORS errors**: Verify `FRONTEND_URL` environment variable matches deployment URL
3. **Invalid tokens**: Backend validates JWT and extracts user context properly
4. **Response parsing**: API responses use nested format that must be parsed correctly
- Fix image display persistence after browser refresh and
      add forgot password feature

      This commit resolves multiple critical issues with image display and adds
      password recovery functionality:

      ## Image Display Fixes (Primary)

      1. **Fix OpenAI File Purpose for Images** (backend/api/files.py)
         - Changed image uploads from purpose='assistants' to purpose='vision'
         - OpenAI does not allow downloading files with purpose='assistants'
         - Images now properly downloadable for display in chat interface
         - Documents still use purpose='assistants' for code_interpreter
      compatibility

      2. **Fix Unix Timestamp Conversion** (backend/api/chat.py)
         - Convert OpenAI's Unix timestamp integers to ISO strings
         - Resolves "Jan 21, 1970" date display issue
         - Message timestamps now show correctly as "Just now", "3h ago", etc.

      3. **Fix Image Type Recognition**
      (frontend/src/components/chat/ChatMessage.vue)
         - Updated filter to recognize 'image_file', 'image', and 'image/*'
      types
         - Images no longer appear as "Referenced Files" links
         - Proper image rendering in chat messages

      4. **Remove Auth Requirement from Image Endpoint** (backend/api/files.py)
         - Made /api/files/openai/{file_id} endpoint public
         - Browser <img> tags cannot send JWT auth headers
         - File IDs are secure random tokens from OpenAI (unguessable)

      5. **Fix Image URL Routing**
      (frontend/src/components/chat/ImageDisplay.vue,
      frontend/src/stores/conversations.ts)
         - Changed from relative URLs to absolute backend URLs
         - Uses VITE_API_URL environment variable for cross-origin requests
         - Resolves 404 errors from images trying to load from frontend domain

      ## Forgot Password Feature

      - New frontend views: ForgotPasswordView.vue, ResetPasswordView.vue
      - Email delivery via Gmail SMTP with App Password authentication
      - Secure JWT reset tokens with 1-hour expiry
      - HTML email templates with reset links
      - Password update functionality in backend/api/auth.py

      ## Documentation

      - Added comprehensive docs/ directory with:
        - architecture.md - System architecture and technology stack
        - deployment.md - Deployment procedures and commands
        - authentication.md - Authentication flow details
        - changelog.md - Complete change history
        - TOKEN_OPTIMIZATION.md - Cost management strategies
      - Updated CLAUDE.md with project context and instructions
      - Email setup script: setup_email_secrets.sh

      ## Testing Results

      ✅ Images display immediately after upload
      ✅ Images persist through logout/login cycles
      ✅ Images persist after browser refresh
      ✅ Timestamps display correctly
      ✅ No 404 errors in browser console
      ✅ Forgot password email delivery working