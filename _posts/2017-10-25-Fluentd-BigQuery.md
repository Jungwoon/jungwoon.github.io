---
layout: post
title: "Fluentd에서 BigQuery로 데이터 보내기"
image: '/assets/img/'
description: 'Fluentd에서 BigQuery로 데이터 보내기'
tags:
- BigQuery
- Compute Engine
- Fluentd
categories:
- BigQuery
---


## Fluentd를 이용하여 Twitter의 Feed를 PubSub으로 보내기

Fluentd를 활용하여 Twitter의 Feed를 PubSub으로 보내기

---

### 실습환경

```
Mechine Type : n1-standard-1(vCPU 1개 3.75GB 메모리)
Region : asia-east-1-a
OS : Ubuntu 14.04 LTS
ID 및 API 액세스 : 모든 Cloud API에 대한 전체 액세스 허용
방화벽 : HTTP 트래픽 허용, HTTPS 트래픽 허용
```

---


### 실습

#### Ruby 2.0 이상 설치하기

```
$ sudo apt-get update
$ sudo apt-get install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev python-software-properties libffi-dev
$ cd
$ git clone https://github.com/rbenv/rbenv.git ~/.rbenv
$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
$ echo 'eval "$(rbenv init -)"' >> ~/.bashrc
$ exec $SHELL
$ git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
$ echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
$ exec $SHELL
$ rbenv install 2.4.2
$ rbenv global 2.4.2
$ ruby -v
```

---

#### 버전 정보

```
td-agent - 0.12.40
evnetmachine - 1.2.5
fluent-plugin-gcloud-pubsub - 0.0.5
fluent-plugin-twitter - 0.6.1
twitter - 6.2.0
```

---

#### Fluentd 설치하기

```
$ sudo apt-get update
$ sudo apt-get install build-essential
$ sudo apt-get install libssl-dev
$ curl -L https://toolbelt.treasuredata.com/sh/install-ubuntu-trusty-td-agent2.sh | sh
$ sudo td-agent-gem install eventmachine
$ sudo td-agent-gem install fluent-plugin-gcloud-pubsub
$ sudo td-agent-gem install fluent-plugin-twitter
```

---

#### 그 외 설정파일

```
참고 (tdagent 관련 명령어)

$ sudo /etc/init.d/td-agent start
$ sudo /etc/init.d/td-agent stop
$ sudo /etc/init.d/td-agent restart
$ sudo /etc/init.d/td-agent status
$ sudo vi /etc/td-agent/td-agent.conf
$ tail -f /var/log/td-agent/td-agent.log
```

---

### 설정 파일 

```
~/.bashrc

# Added byJW
alias td-agent='sudo /etc/init.d/td-agent'
alias td-agent.conf='sudo vi /etc/td-agent/td-agent.conf'
alias tdmon='tail -f /var/log/td-agent/td-agent.log'
alias tdgem='sudo td-agent-gem'
```

```
~/.vimrc

set nu
set ts=4
set shiftwidth=4
set laststatus=2
```

```
/etc/td-agent/td-agent.conf

<source>
    @type twitter
    consumer_key    lvN5uFPnxBKImldP3rrmHq2vh
    consumer_secret         kY0SAww8kRQhTA6Pp044JzCIMwSXdLyF2RKu71eam54iGtwvDj
    access_token     2157919489-duFYYM21tjMWmMLxz1QA29U2L8mzmVZ9UkfZlsP
    access_token_secret      ovRtUMrfjOB9dV7CYnzZy8IUI2DhOWtP0zJtokTrmgYxz
    tag     input.twitter
    timeline        sampling
    keyword         iPhoneX
    output_format   nest
</source>

### for Standout

<match input.twitter>
    @type stdout
</match>

### for Cloud Pub/Sub

#<match input.twitter>
#   @type gcloud_pubsub
#   project beer-coding
#   topic projects/beer-coding/topics/twitter
#   key /home/byjungwoon/key.json
#   flush_interval 10
#   autocreate_topic false
#</match>
```

---

### 이 당시 설치해 놓은 Gem 버전들

```
*** LOCAL GEMS ***

actionmailer (4.2.8)
actionpack (4.2.8)
actionview (4.2.8)
activejob (4.2.8)
activemodel (4.2.8)
activerecord (4.2.8)
activesupport (4.2.8)
addressable (2.5.2, 2.5.1)
arel (6.0.4)
autoparse (0.3.3)
aws-sdk (2.10.45)
aws-sdk-core (2.10.45)
aws-sdk-resources (2.10.45)
aws-sigv4 (1.0.2)
beefcake (1.2.0)
bigdecimal (default: 1.2.4)
bson (4.1.1)
buftok (0.2.0)
builder (3.2.3)
bundler (1.14.5)
celluloid (0.15.2)
cool.io (1.5.1)
diff-lcs (1.3)
digest-crc (0.4.1)
domain_name (0.5.20170404)
draper (1.4.0)
equalizer (0.0.11)
erubis (2.7.0)
eventmachine (1.2.5)
extlib (0.9.16)
faraday (0.13.1)
fluent-logger (0.7.1)
fluent-mixin-plaintextformatter (0.2.6)
fluent-plugin-buffer-lightening (0.0.2)
fluent-plugin-gcloud-pubsub (0.0.5)
fluent-plugin-kafka (0.6.1)
fluent-plugin-mongo (0.8.1)
fluent-plugin-rewrite-tag-filter (1.5.6)
fluent-plugin-s3 (0.8.5)
fluent-plugin-scribe (0.10.14)
fluent-plugin-td (0.10.29)
fluent-plugin-td-monitoring (0.2.3)
fluent-plugin-twitter (0.6.1)
fluent-plugin-webhdfs (0.7.1)
fluentd (0.14.22, 0.12.40)
fluentd-ui (0.4.4)
font-awesome-rails (4.7.0.1)
gcloud (0.6.3)
globalid (0.4.0)
google-api-client (0.8.7)
googleauth (0.6.1)
haml (4.0.7)
haml-rails (0.5.3)
hike (1.2.3)
hirb (0.7.3)
http (3.0.0)
http-cookie (1.0.3)
http-form_data (2.0.0)
http_parser.rb (0.6.0)
httpclient (2.8.2.4)
i18n (0.8.1)
io-console (default: 0.4.3)
ipaddress (0.8.3)
jbuilder (2.6.3)
jmespath (1.3.1)
jquery-rails (3.1.4)
json (default: 1.8.1)
jwt (2.1.0)
kramdown (1.13.2)
kramdown-haml (0.0.3)
launchy (2.4.3)
little-plugger (1.1.4)
logging (2.2.2)
loofah (2.0.3)
ltsv (0.1.0)
mail (2.6.4)
memoist (0.16.0)
memoizable (0.4.2)
mime-types (3.1, 2.99.3)
mime-types-data (3.2016.0521)
mini_portile2 (2.3.0, 2.1.0)
minitest (5.10.1, default: 4.7.5)
mixlib-cli (1.7.0)
mixlib-config (2.2.4)
mixlib-log (1.7.1)
mixlib-shellout (2.2.7)
mongo (2.2.7)
msgpack (1.1.0)
multi_json (1.12.1)
multipart-post (2.0.0)
naught (1.1.0)
nokogiri (1.8.1)
ohai (6.20.0)
oj (2.18.1)
os (0.9.6)
parallel (1.8.0)
psych (default: 2.0.5)
public_suffix (3.0.0, 2.0.5)
puma (3.8.2)
rack (1.6.5)
rack-test (0.6.3)
rails (4.2.8)
rails-deprecated_sanitizer (1.0.3)
rails-dom-testing (1.0.8)
rails-html-sanitizer (1.0.3)
railties (4.2.8)
rake (default: 10.1.0)
rdoc (default: 4.1.0)
request_store (1.3.2)
retriable (1.4.1)
ruby-kafka (0.4.2)
ruby-progressbar (1.8.3)
ruby_dig (0.0.2)
rubyzip (1.2.1, 1.1.7)
sass (3.2.19)
sass-rails (4.0.5)
serverengine (2.0.5)
settingslogic (2.0.9)
sigdump (0.2.4)
signet (0.8.1)
simple_oauth (0.3.1)
sprockets (2.12.4)
sprockets-rails (2.3.3)
string-scrub (0.0.5)
strptime (0.1.9)
sucker_punch (1.0.5)
systemu (2.5.2)
td (0.15.2)
td-client (0.8.85)
td-logger (0.3.27)
test-unit (default: 2.1.10.0)
thor (0.19.4)
thread_safe (0.3.6)
thrift (0.8.0)
tilt (1.4.1)
timers (1.1.0)
twitter (6.2.0)
tzinfo (1.2.3)
tzinfo-data (1.2017.2)
unf (0.1.4)
unf_ext (0.0.7.4)
uuidtools (2.1.5)
webhdfs (0.8.0)
yajl-ruby (1.3.0)
zip-zip (0.3)
zonefile (1.06)
```