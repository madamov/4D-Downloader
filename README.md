# 4D Downloader

4D Downloader is a small desktop utility for downloading authenticated 4D installer archives from [product-download.4d.com](https://product-download.4d.com).

The 4D download site is built with Drupal and requires an interactive account login. Consequently, downloading protected installer URLs with `curl`, even when supplying a username and password and enabling redirects with `-L`, does not work as it would with HTTP Basic authentication. 4D Downloader solves this by using an embedded browser to sign in to the 4D account site and download the requested archives through the authenticated browser session.

The graphical interface is also a demonstration of what can be built with [Objo Studio](https://objo.dev/).

## Features

- Signs in with a 4D account through the official 4D login page.
- Downloads one or more installer archive URLs in sequence.
- Provides a GUI for entering credentials and managing the download list.
- Can save a download list as JSON for later use.
- Supports command-line operation for scripted downloads.
- Displays download progress.
- Saves archives to the current user's **Downloads** folder.
- Logs out of the 4D account after the queue is complete.

## Requirements

- A valid 4D account with permission to download the requested products.
- Network access to `account.4d.com` and `product-download.4d.com`.

## Using the GUI

1. Launch **4D Downloader**.
2. Enter your 4D account email and password.
3. Open the downloader from the application menu.
4. Add the full URL of each installer archive you want to download.
5. Optionally save the list as a JSON file.
6. Start the download.

The application signs in, processes each URL in order, and saves the files to your Downloads folder. It closes the downloader window and reports completion when the queue is finished.

## Command-line usage

The built application accepts a 4D username, password, and a JSON array containing the archive URLs:

```sh
./4D\ Downloader \
  -username "name@example.com" \
  --password "your-password" \
  --filelist '["https://product-download.4d.com/path/to/archive.dmg"]'
```

Short option names are also available:

```sh
./4D\ Downloader \
  -u "name@example.com" \
  -p "your-password" \
  -l '["https://product-download.4d.com/path/to/archive-1.dmg","https://product-download.4d.com/path/to/archive-2.dmg"]'
```

> **Security note:** Supplying a password directly on the command line may expose it through shell history or the operating system's process list. Use secrets in CI/CD workflows. Prefer the GUI when that is a concern.

## Opening the source project

Clone the repository, then open `4D Downloader.objosln` in Objo Studio. The main application project is located in `Projects/4D Downloader`.

The application flow is implemented by four principal classes:

- `App` handles startup, GUI/CLI selection, arguments, and shared state.
- `CredentialsWindow` collects the 4D account credentials.
- `FilesAddWindow` builds and optionally saves the URL list.
- `DownloaderWindow` performs browser login and sequential downloads.

## How it works

```text
Credentials + archive URLs
            |
            v
 Embedded browser opens account.4d.com
            |
            v
 Authenticated session opens product-download.4d.com
            |
            v
 Archive URLs are downloaded sequentially
            |
            v
 Files are saved to the Downloads folder, then the session logs out
```

## Disclaimer

This is an independent utility and is not affiliated with or endorsed by 4D SAS. Use it only with an account and installer archives you are authorized to access. “4D” and related marks belong to their respective owners.
