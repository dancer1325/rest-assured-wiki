# How to release #

  1. Update changelog with the date of the new release
  1. Run `./mvn_release.sh` and follow the instructions.
    1. If build fails because of missing dependencies do:
      1. `mvn install -Prelease,dist`
      1. `mvn release:prepare -Prelease,dist -Dresume`
      1. `mvn release:perform -Prelease,dist`
  1. Log in to [Sonatype](https://oss.sonatype.org).
  1. Follow the [Sonatype release directions](https://docs.sonatype.org/display/Repository/Sonatype+OSS+Maven+Repository+Usage+Guide) in bullet 8.
    * Remove all dist projects
    * If sonatype complains about missing javadoc from scalatra, remove the scaltra-example project from the sonatype browser.
  1. Upload the artifacts to [bintray](http://bintray.com).
    1. Run the `./deploy_bintray.sh` script and enter the API Key (found on Bintray on the user profile page when pressing the edit button) and version to release.
    1. Login to [bintray](http://bintray.com) and add release notes and publish the release.
  1. Generate javadoc by running the `./generate_javadoc.sh` script and enter the version to release
  1. Update the wiki by running the `./update_wiki.sh` script and enter the previous version and the new version to release. This will clone the wiki from GitHub and replace `old version` to `new version` on all pages. Afterwards you have the script offers you to commit and push the changes. If you want to avoid having to enter the github username and password each time see [this](https://help.github.com/articles/caching-your-github-password-in-git/) page.
  1. Update the `README.md` file to indicate that a new version was released
  1. Send a message to the mailing-list and twitter announcing the new release.

The release is automatically synced to Maven central (the sync process runs hourly).
