# alpine base rsync and ssh-client

Docker image based on alpine with ssh-client, rsync and bash for upload/deploying project

## Supported Tags

- latest

## Yii2 Gitlab CI Example

```yml
#gitlab-ci.yml
deploy:
  stage: deploy
  image: shyevsa/ssh-rsync
  only:
    - dev
  before_script:
    - mkdir -p ~/.ssh
    - eval $(ssh-agent -s)
    - echo -e "Host *\n\t StrictHostKeyChecking no\n\n" > ~/.ssh/config

  script:
    - ssh-add <(echo "$STAGING_PRIVATE_KEY")
    - rsync -avz --no-perms --delete --exclude-from=deploy-exclude.txt . $STAGING__HOST:~/app
    - ssh $STAGING_HOST 'cd ~/app && composer install --no-progress --optimize-autoloader --no-ansi --no-interaction --prefer-dist --no-dev'
    - ssh $STAGING_HOST 'cd ~/app && php init --env=Staging --overwrite=All'
    - ssh $STAGING_HOST 'cd ~/app && php yii migrate/up --interactive=0'
  when: manual
```

## From Docker

`docker run -it --rm shyevsa/ssh-rsync`
