<p align="center">
  <a href="https://dev.to/vumdao">
    <img alt="Config Gitlab Runner Able To Push A Tag" src="https://dev-to-uploads.s3.amazonaws.com/i/i5ogkpnoun1qnztyoddk.jpg" width="500" />
  </a>
</p>
<h1 align="center">
  <div><b>Config Gitlab Runner To Push A Tag</b></div>
</h1>

<h2>
Any commits should be tagged align with build version especially `master` branch. How to configure Gitlab runner to do this?
</h2>

## What’s In This Document 
- [What is the usecase](#-What-is-the-usecase)
- [Solve problem by using OAUTH2.0 and tokens](#-Solve-problem-by-using-OAUTH2.0-and-tokens)

### 🚀 **[What is the usecase](#-What-is-the-usecase)**
- Developer tells gitlab runner to tag the commit and publish the tag
[.gitlab-ci.yml]()
```
build:
  stage: build
  script:
    - echo "Build and tag the commit"
    - tag=1.0-${CI_COMMIT_SHORT_SHA}
    - git tag $tag
    - git push origin $tag
  tags:
    - gitlab-runner
```

- But got the error `remote: You are not allowed to upload code.`
```
Build and tag the commit
$ tag=1.0-${CI_COMMIT_SHORT_SHA}
$ git tag $tag
$ git push origin $tag
remote: You are not allowed to upload code.
fatal: unable to access 'https://gitlab-ci-token:[MASKED]@gitlab.com/hello-gitlab.git/': The requested URL returned error: 403
ERROR: Job failed: exit status 1
```

### 🚀 **[Solve problem by using OAUTH2.0 and tokens](#-Solve-problem-by-using-OAUTH2.0-and-tokens)**
#### **1. Create access token**
- Go to `User Setttings` -> `Access Tokens`

![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/4mve0fn40k5wnkp2e6cp.png)

![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/oul9w4o5ebpobo948aw2.png)

#### **2. Update [.gitlab-ci.yaml](https://github.com/vumdao/gitlab-tag/.gitlab-ci.yaml)**
```
build:
  stage: build
  before_script:
    - project_url=$(echo $CI_PROJECT_URL | sed 's/https:\/\///')
    - git remote set-url origin https://oauth2:$GITLAB_TOKEN@$project_url
  script:
    - echo "Build and tag the commit"
    - tag=1.0-${CI_COMMIT_SHORT_SHA}
    - git tag $tag
    - git push origin $tag
  only:
    refs:
      - tagme
  tags:
    - gitlab-runner
```

#### **3. Check result**
```
Build and tag the commit
$ tag=1.0-${CI_COMMIT_SHORT_SHA}
$ git tag $tag
$ git push origin $tag
warning: redirecting to https://gitlab.com/hello-gitlab.git/
To https://gitlab.com/hello-gitlab
 * [new tag]           1.0-0714997f -> 1.0-0714997f
 Job succeeded
```

#### **Ref: [Make pipeline permissions more controllable and flexible](https://gitlab.com/groups/gitlab-org/-/epics/3559)**

### **Visit wwww.cloudopz.co to get more**


<h3 align="center">
  <a href="https://dev.to/vumdao">:stars: Blog</a>
  <span> · </span>
  <a href="https://vumdao.hashnode.dev/">Web</a>
  <span> · </span>
  <a href="https://www.linkedin.com/in/vu-dao-9280ab43/">Linkedin</a>
  <span> · </span>
  <a href="https://www.linkedin.com/groups/12488649/">Group</a>
  <span> · </span>
  <a href="https://www.facebook.com/CloudOpz-104917804863956">Page</a>
  <span> · </span>
  <a href="https://twitter.com/VuDao81124667">Twitter :stars:</a>
</h3>