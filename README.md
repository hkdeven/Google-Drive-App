## Ruby Quickstart

Complete the steps described in the rest of this page, and in about five minutes you'll have a simple Ruby command-line application that makes requests to the Drive API.

### Prerequisites

To run this quickstart, you'll need:

- Ruby 2.0 or greater.
- Access to the internet and a web browser.
- A Google account with Google Drive enabled.

### Step 1: Turn on the Drive API

1. Use [this wizard](https://console.developers.google.com/start/api?id=drive) to create or select a project in the Google Developers Console and automatically turn on the API. Click **Continue**, then **Go to credentials**.
2. On the **Add credentials to your project** page, click the **Cancel** button.
3. At the top of the page, select the **OAuth consent screen** tab. Select an **Email address**, enter a **Product name** if not already set, and click the **Save** button.
4. Select the **Credentials** tab, click the **Create credentials** button and select **OAuth client ID**.
5. Select the application type **Other**, enter the name "Drive API Quickstart", and click the **Create** button.
6. Click **OK** to dismiss the resulting dialog.
7. Click the file_download (Download JSON) button to the right of the client ID.
8. Move this file to your working directory and rename it 

client_secret.json.

### Step 2: Install the Google Client Library

Run the following command to install the library:

gem install google-api-client

See the library's [installation page](https://developers.google.com/api-client-library/ruby/start/installation) for the alternative installation options.

### Step 3: Set up the sample

Create a file named 

quickstart.rb in your working directory and copy in the following code:

require'google/apis/drive_v3'require'googleauth'require'googleauth/stores/file_token_store'require'fileutils'

OOB_URI ='urn:ietf:wg:oauth:2.0:oob'
APPLICATION_NAME ='Drive API Ruby Quickstart'
CLIENT_SECRETS_PATH ='client_secret.json'
CREDENTIALS_PATH =File.join(Dir.home,'.credentials',
                             "drive-ruby-quickstart.yaml")
SCOPE =Google::Apis::DriveV3::AUTH_DRIVE_METADATA_READONLY

### Ensure valid credentials, either by restoring from the saved credentials# files or intitiating an OAuth2 authorization. If authorization is required,# the user's default browser will be launched to approve the request.## @return [Google::Auth::UserRefreshCredentials] OAuth2 credentialsdef authorize
  FileUtils.mkdir_p(File.dirname(CREDENTIALS_PATH))

  client_id =Google::Auth::ClientId.from_file(CLIENT_SECRETS_PATH)
  token_store =Google::Auth::Stores::FileTokenStore.new(file: CREDENTIALS_PATH)
  authorizer =Google::Auth::UserAuthorizer.new(
    client_id, SCOPE, token_store)
  user_id ='default'
  credentials = authorizer.get_credentials(user_id)
  if credentials.nil?
    url = authorizer.get_authorization_url(
      base_url: OOB_URI)
    puts "Open the following URL in the browser and enter the "+
         "resulting code after authorization"
    puts url
    code = gets
    credentials = authorizer.get_and_store_credentials_from_code(
      user_id: user_id, code: code, base_url: OOB_URI)
  end
  credentials
end# Initialize the API
service =Google::Apis::DriveV3::DriveService.new
service.client_options.application_name = APPLICATION_NAME
service.authorization = authorize
# List the 10 most recently modified files.
response = service.list_files(page_size:10,
                              fields:'nextPageToken, files(id, name)')
puts 'Files:'
puts 'No files found'if response.files.empty?
response.files.each do|file|
  puts "#{file.name} (#{file.id})"end

### Step 4: Run the sample

Run the sample using the following command:

ruby quickstart.rb

The first time you run the sample, it will prompt you to authorize access:

1. The sample will attempt to open a new window or tab in your default browser. If this fails, copy the URL from the console and manually open it in your browser. If you are not already logged into your Google account, you will be prompted to log in. If you are logged into multiple Google accounts, you will be asked to select one account to use for the authorization.
2. Click the **Accept** button.
3. The sample will proceed automatically, and you may close the window/tab.
