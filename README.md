# howSitesAreBackedUpToThisRespository

* ok, so we have so far: a perfunctory web UI, a "sitesToBackupCollection" which stores data for each site, and are part of the way through making the thingy which runs the backup procedure for each site in the collection. Currently that procedure - creates or reads a github repository for a site, clones the repo onto disk, reads the sitemap.json for the site, and cycles through all the slug.jsons in the sitemap, downloading them and adding them to the /data directory of the github repository.  it also deletes the local repo "when finished".
* this is all done defensively, errors are "all" caught, and recorded, most of the way there to dealing with errors in a "sensible" way for an always on backup machine that will recover gracefully in situations of "because internets"
* fetching from the remote server is done gently.
* the tool is built such that multiple site backups can run simultaneously if desired.

### Things left to do for a first attempt
* I am currently using "nodegit" which is an interface to the libgit2 library, natively in node.js. It seems that it is very complicated to use, and has powers way beyond our needs. I am going to ditch that and simply call use a shell to control "command line" git from node. (I already do this in some other use cases. I wanted to try out nodegit).
* use command line git to "git add -A" and "git commit -m" and "git push origin master".  Specifically "git add -A" does a _lot_ of different things, which it is most complicated to try and copy with nodegit.
* unleash the thing on a bigger list of sites (currently only backs up a single site).
* check for slugs that have been deleted from the site (does this happen)?

### Things to do later
* work out how to squash commits so that there is a nice coherent history of daily / weekly / monthly / etc... commit history.
