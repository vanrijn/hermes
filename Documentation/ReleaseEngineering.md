Release Engineering
===================

Requirements
------------

- The DSA private key to generate a signature. This signature is used by
  Sparkle to prove the integrity and authenticity of the release archive (zip
  file). The DSA private key should be accessible (can be a symlink) as
  `~/Documents/hermes.key`.
- S3 credentials to upload the release archive to Alex's S3 bucket.
- Copies of HermesApp/Hermes (the app repo) and the
  HermesApp/HermesApp.github.io (the GitHub pages repo) in the **same**
  directory. The GitHub pages repo **must** be accessible as `hermes-pages`.

The Process
-----------

1. Edit `Hermes/Resources/Info.plist`:
    1. Increment "Bundle versions string, short"
       (`CFBundleShortVersionString`).
    2. Increment "Bundle version" (`CFBundleVersion`).

2. Edit `Hermes/CHANGELOG.md` ensuring all significant improvements are noted,
   and change the link to "Full Changelog" to the differences between last
   release and the release that is about to be pushed to GitHub.

3. Test everything, ensuring it's all working as expected. Once satisfied,
   commit the changes made in the last two steps. Once committed, tag the
   commit with the release version. For example:

        git tag v1.2.0 750f2de


4. Compile, archive, upload the new version, and edit the `hermes-pages` site:

        make upload-release

5. Ensure the GitHub pages repository was edited correctly:
    1. Inspect `hermes-pages/_data/urls.yml` ensuring the URL for key
       `hermes_download` is valid.
    2. Ensure `hermes-pages/versions.xml` is valid XML.
    3. Make sure `hermes-pages/CHANGELOG.md` looks like `Hermes/CHANGELOG.md`.

6. Commit changes in `hermes-pages` and push to GitHub:

        cd hermes-pages
        git add .
        git commit -m v1.2.0
        git push origin master

7. Try updating from an old version of Hermes. From textual menus:
   **Hermes → Check for Updates**; or from the statusbar icon: click on the
   icon, then hold down option and click **Check for Updates**. If the update
   results in an error, open `~/Library/Logs/SparkleUpdateLog.log` and find
   out what went wrong. If necessary start over, preferrably removing the bad
   commit in `github-pages` with command `git reset --hard HEAD^`.

8. Push *both* Hermes/HermesApp's master branch and the newly made git tag:

        cd Hermes
        git push origin master
        git push origin v1.2.0
