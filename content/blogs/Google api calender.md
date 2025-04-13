Here’s a step-by-step blog-style guide on **creating Google Calendar events in Ruby on Rails using a service account with user impersonation**:

**🔧 Step-by-Step: Create Google Calendar Events via Rails Console with Service Account**

  

**✅ Step 1: Enable Google Calendar API**

• Go to [Google Cloud Console](https://console.cloud.google.com/).

• Create a project (or select an existing one).

• Enable **Google Calendar API** under **APIs & Services > Library**.

**✅ Step 2: Create a Service Account**

• Go to **APIs & Services > Credentials**.

• Click **Create Credentials > Service account**.

• Grant it any name, assign “Editor” or appropriate role.

• After creation, click **“Add Key” > JSON**, and download the key file.

**✅ Step 3: Enable Domain-Wide Delegation**

• In the service account settings, enable **“Domain-wide delegation”**.

• Note down the **Client ID**.

**✅ Step 4: Grant Delegation in Google Workspace**

  

(Only if you use a Google Workspace domain)

• Go to **admin.google.com > Security > API Controls > Manage domain-wide delegation**.

• Add a new API client:

• **Client ID**: from Step 3

• **Scopes**: https://www.googleapis.com/auth/calendar

**✅ Step 5: Add Gems**

  

In your Rails app, ensure you have these gems in Gemfile:

```
gem 'googleauth'
gem 'google-apis-calendar_v3'
```

Then run:

```
bundle install
```

**✅ Step 6: Authenticate and Impersonate**

  

In Rails console (rails c):

```
require 'google/apis/calendar_v3'
require 'googleauth'

credential_file = 'path/to/your-service-account.json'

auth = Google::Auth::ServiceAccountCredentials.make_creds(
  json_key_io: File.open(credential_file),
  scope: Google::Apis::CalendarV3::AUTH_CALENDAR
)

auth.sub = "youremail@yourdomain.com"  # user to impersonate
auth.fetch_access_token!
```

**✅ Step 7: Initialize Calendar Service**

```
service = Google::Apis::CalendarV3::CalendarService.new
service.authorization = auth
```

**✅ Step 8: Create and Insert an Event**

```
event = Google::Apis::CalendarV3::Event.new(
  summary: 'Test Event',
  location: 'Online',
  description: 'Testing event creation via Rails console',
  start: { date_time: '2025-04-12T10:00:00+05:30' },
  end: { date_time: '2025-04-12T11:00:00+05:30' },
  attendees: [{ email: 'sandipparida282@gmail.com' }],
  reminders: { use_default: true }
)

calendar_id = 'primary' # or specific calendar ID
service.insert_event(calendar_id, event)
```

**✅ Step 9: 🎉 Done!**

