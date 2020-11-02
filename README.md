# howSitesAreBackedUpToThisAccount

* ok, so we have so far
  * a slightly less perfunctory web UI
  * a `sitesToBackupCollection` 
    * which stores data for each site
    * `siteSpec.siteURL`
    * `backupThisSite.githubRepoData`
  * a `applicationConfiguration.backupThisSite.siteDataSummaryBySiteNameDict`
    * which contains timestamps for `lastBackupAttempted` and `lastBackupCompleted` for each site
  * we are part of the way through making the sequence which runs the backup procedure for each site in the collection. Currently that procedure
  * with `githubAPI`, creates or reads a (public) github repository for a site (in this github account)
    * ~clones the repo onto the disk of the backup server~
    * with `githubAPI` read the sitemap.json stored in the github repo
      * and to read the list of files in the repo to use as comparison
    * reads the current sitemap.json from the siteURL `/system/sitemap.json`
    * compares between the `current sitemap.json` and the `github sitemap.json`, and also the `file tree on github`, to determine `add` / `update` / `delete` of pages
    * with `githubAPI`,  `adds` / `updates` / `deletes` pages on a new github branch `newBranch`
      * downloading new page data from siteURL
        * 4000 ms delay between each page download
        * back off 2s, 4, 8, 16, 32s if http errors are encountered
          * if the 32s attempt fails (about 1 minute of trying in total), the backup is aborted and will be retried "soon"
    * with `githubAPI` uploads the new sitemap.json to `newBranch`
    * with `githubAPI` squash merges `newBranch` into the `main` branch of the github repo
    * with `githubAPI` deletes `newBranch` from the repo
    * if no updates are necessary, no change is made to the github repo
    * ~cycles through all the slug.jsons in the sitemap~
      * ~downloading them and adding them to the /data directory of the local git repository~
      * ~currently does not delete pages that have been removed from the wiki site~
      * ~removes pages that havebeen deleted from the wiki site~
    * ~makes a new git commit and pushes the commit to the github repository~
    * ~deletes the local repo when finished~

* this is all done defensively, errors are "all" caught, and recorded, most of the way there (all the way there?) to dealing with errors in a "sensible" way for an always on backup machine that will recover gracefully in situations of "because internets"
  * assuming that the context that the backup server is running in doesnt break, the backup server should function as expected in all situations, and "pick up where it left off" regardless of internet outages

* hourly backup of all sites
* backups run on 5 concurrent threads

* link to DEMO video so far <a href="https://youtu.be/lQPiEFRNrFs"><img src="https://img.youtube.com/vi/lQPiEFRNrFs/maxresdefault.jpg" width="200"/></a>

### Things left to do for a first attempt
* [x] - ~I am currently using "nodegit" which is an interface to the libgit2 library, natively in node.js. It seems that it is very complicated to use, and has powers way beyond our needs. I am going to ditch that and simply call use a shell to control "command line" git from node. (I already do this in some other use cases. I wanted to try out nodegit).~
* [x] - use command line git to "git add -A" and "git commit -m" and "git push origin master".  Specifically "git add -A" does a _lot_ of different things, which it is most complicated to try and copy with nodegit.
* [x] - check for pages that have been deleted from the site but were already backed up (does this happen)?
* [ ] - write log to log repo
* [x] - unleash the thing on a bigger list of sites (currently only backs up a single site).
  * currently running on 30 sites out of the 750 or so in the list that I have
* [x] - make cron type stuff
* [x] - make it run headless

### Things to do later
* [ ] - work out how to squash commits so that there is a nice coherent history of daily / weekly / monthly / etc... commit history.
* [ ] - implement some kind of email notification system for when things go wrong
