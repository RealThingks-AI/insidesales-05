

## Update Email URL and Build Email Center Sections

### 1. Fix Email URL in Edge Function

**File: `supabase/functions/daily-action-reminders/index.ts`**
- Change `const appUrl = 'https://crm-rt-4.lovable.app'` to `const appUrl = 'https://crm.realthingks.com'`

### 2. Build Email Center -- Templates Tab

The current Email Center has three placeholder tabs. The reference files show full implementations, but they depend on database tables (`email_templates`, `email_history`, `email_replies`) and components (`RichTextEditor`, `useProfiles`, `EmailReplyModal`, `OutlookEmailBody`, `emailUtils`) that don't exist in the current project.

**Approach:** Build simplified but functional versions that work with the existing codebase.

#### Database: Create `email_templates` table
```sql
CREATE TABLE email_templates (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  name text NOT NULL,
  subject text NOT NULL,
  body text NOT NULL,
  created_by uuid REFERENCES auth.users(id),
  created_at timestamptz DEFAULT now(),
  updated_at timestamptz DEFAULT now()
);
ALTER TABLE email_templates ENABLE ROW LEVEL SECURITY;
-- RLS: authenticated users can CRUD
```

#### New Files:
- **`src/components/settings/EmailTemplatesSettings.tsx`** -- CRUD for email templates (based on reference file, simplified to use a `<Textarea>` instead of `RichTextEditor`, and `StandardPagination` instead of `TablePagination`). Features: create/edit/delete/duplicate/preview templates with variable support (`{{contact_name}}`, `{{company_name}}`, etc.)
- **`src/components/settings/email/TemplatePreviewModal.tsx`** -- Preview modal showing template with sample data (copy directly from reference)

#### Update:
- **`src/components/settings/EmailCenterPage.tsx`** -- Replace placeholder tabs with lazy-loaded `EmailTemplatesSettings`. Keep History and Analytics as "coming soon" since they need `email_history` table and email sending infrastructure.

### 3. Build Email Center -- History Tab (Simplified)

Since `email_history` and email sending infrastructure don't exist, show a clear informational state explaining what this section will track once email sending is configured, rather than a vague "coming soon".

### 4. Build Email Center -- Analytics Tab (Simplified)

Same approach -- show an informational empty state with metrics descriptions.

### Summary

| Change | File |
|--------|------|
| Fix URL | `supabase/functions/daily-action-reminders/index.ts` |
| Create table | Migration for `email_templates` |
| Templates CRUD | `src/components/settings/EmailTemplatesSettings.tsx` |
| Preview modal | `src/components/settings/email/TemplatePreviewModal.tsx` |
| Update center | `src/components/settings/EmailCenterPage.tsx` |
| History placeholder | Better empty state in EmailCenterPage |
| Analytics placeholder | Better empty state in EmailCenterPage |

