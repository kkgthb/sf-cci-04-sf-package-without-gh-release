"[When you’re ready to release the package, you create a snapshot of it, called a package version.  ...  Remember, once created, a package version serves as an immutable artifact containing a specific set of metadata.](https://trailhead.salesforce.com/content/learn/modules/unlocked-packages-for-customers/build-your-first-unlocked-package)"

```
sfdx force:package:create --name just-the-packs-maam --description "Just the Packs Maam" --packagetype Unlocked --path force-app --nonamespace --targetdevhubusername a_clever_sfdx_org_alias
sfdx-project.json has been updated.
Successfully created a package. 0Ho3t000000k9otCAA
=== Ids
 Name       Value
 ────────── ────────────────── 
 Package Id 0Ho3t000000k9otCAA
```

```
sfdx force:package:version:create -x -v a_clever_sfdx_org_alias -p just-the-packs-maam -d force-app
ERROR running force:package:version:create:  VersionNumber must be in the format major.minor.patch.build but the value found is [0.001].
```

```
sfdx force:package:version:create -k top_secret_ultra_cool_key -v a_clever_sfdx_org_alias -p just-the-packs-maam -d force-app
=== Package Version Create Request
 Name                          Value
 ───────────────────────────── ───────────────────────────────────────────────────────────────────────────────── 
 ID                            08c...
 Status                        Success
 Package Id                    0Ho3t000000k9otCAA
 Package Version Id            05i...
 Subscriber Package Version Id 04t3t000000jG7SAAU
 Tag
 Branch
 Created Date                  2022-09-27 15:24
 Installation URL              https://login.salesforce.com/packaging/installPackage.apexp?p0=04t3t000000jG7SAAU
 Created By                    005...
```

It installs great from `cci flow run dev_org` in the scope of another package whose `cumulusci.yml` looked like this:

```yaml
project:
  # ...
  dependencies:
    - version_id: '04t3t000000jG7SAAU'
```

Updated `sfdx-project.json` throughout this process and am now committing it to this repo.  Let's see what happens with the other package's `cumulusci.yml` like this now:

```yaml
project:
  # ...
  dependencies:
    - github: 'https://github.com/kkgthb/sf-cci-04-sf-package-without-gh-release'
```

Yeah, builds just fine from the most-recent-commit of this repo _(which doesn't have any GitHub releases associated with it)_ -- I can tell by the commit number.

It seems that:

1. `- version_id: ...` goes straight out to Salesforce and ignores the existence of a GitHub repo that I, personally, just so happen to know had anything to do with that release.
2. `- github: ...` doesn't try to "get clever" and parse the `sfdx-project.json` it finds and comb for "`04t...`" Salesforce Package Version IDs or anything.  If there's no "GitHub Release" telling CumulusCI what to do to find a `04t...`" Salesforce Package Version ID, CumulusCI seems not to care about the existence of a "`04t...`" value in `sfdx-project.json` and just treats this repo exactly the same way that it treats [sf-cci-01-minimum-viable-build](https://github.com/kkgthb/sf-cci-01-minimum-viable-build).