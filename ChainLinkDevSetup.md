# Set up a ChainLink Development Environment

## Install required packages

```script
sudo apt install build-essential git libssl1.0-dev libssl-dev libxml2-dev libxslt1-dev postgresql-9.6 libpq-dev nodejs
```

## Set up Ruby

```script
git clone git://github.com/sstephenson/rbenv.git $HOME/.rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> $HOME/.bashrc
echo 'eval "$(rbenv init -)"' >> $HOME/.bashrc
exec $SHELL -l
git clone git://github.com/sstephenson/ruby-build.git $HOME/.rbenv/plugins/ruby-build
rbenv install 2.3.1
rbenv rehash
rbenv global 2.3.1
ruby -v
```

## Set up PostgreSQL

```script
su -
su postgres
psql
CREATE ROLE thomas LOGIN INHERIT;
ALTER ROLE thomas CREATEDB;
\q
exit
exit
```

## Finally get and install ChainLink

```script
git clone https://github.com/smartcontractkit/chainlink.git && cd chainlink
gem install bundler && bundle
rake db:create db:migrate
```
