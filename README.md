# howSitesAreBackedUpToThisAccount

* ok, so we have so far
  * a slightly less perfunctory web UI
  * a `sitesToBackupCollection` 
    * which stores data for each site
  * we are part of the way through making the sequence which runs the backup procedure for each site in the collection. Currently that procedure:
    * creates or reads a (public) github repository for a site (in this github account)
    * clones the repo onto the disk of the backup server
    * reads the sitemap.json for the site
    * cycles through all the slug.jsons in the sitemap
      * downloading them and adding them to the /data directory of the github repository
      * currently does not delete pages that have been removed from the wiki site
    * makes a new git commit and pushes the commit to the github repository
    * deletes the local repo "when finished".

* this is all done defensively, errors are "all" caught, and recorded, most of the way there to dealing with errors in a "sensible" way for an always on backup machine that will recover gracefully in situations of "because internets"

* fetching from the remote server is done gently (300ms time delay)
  * http errors are dealth with in a "backoff" fashion 2, 4, 8, 16, 32, 64 seconds

* the tool is built such that multiple site backups can run simultaneously if desired

* link to DEMO video so far <a href="https://youtu.be/lQPiEFRNrFs"><img src="https://img.youtube.com/vi/lQPiEFRNrFs/maxresdefault.jpg" width="200"/></a>

### Things left to do for a first attempt
* [x] ~I am currently using "nodegit" which is an interface to the libgit2 library, natively in node.js. It seems that it is very complicated to use, and has powers way beyond our needs. I am going to ditch that and simply call use a shell to control "command line" git from node. (I already do this in some other use cases. I wanted to try out nodegit).~
* [x] use command line git to "git add -A" and "git commit -m" and "git push origin master".  Specifically "git add -A" does a _lot_ of different things, which it is most complicated to try and copy with nodegit.
* check for pages that have been deleted from the site but were already backed up (does this happen)?
* unleash the thing on a bigger list of sites (currently only backs up a single site).


### Things to do later
* work out how to squash commits so that there is a nice coherent history of daily / weekly / monthly / etc... commit history.
