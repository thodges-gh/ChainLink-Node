# Set up a ChainLink Development Environment

## Install required packages

```script
# Ensure that your system is up to date and fully patched
sudo apt update -y && sudo apt upgrade -y

# Install dependencies
sudo apt -y install build-essential git libssl1.0-dev libssl-dev libxml2-dev libxslt1-dev postgresql-9.6 libpq-dev nodejs
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

## Testing
 
To run the full test suite, including integration tests, you need an instance of DevNet running on your machine. This requires first installing [Parity](https://github.com/paritytech/parity). Once Parity is installed, run the following commands:

```
git clone https://github.com/smartcontractkit/devnet.git
cd devnet
./start
```

Then to run the full test suite run:
 ```
 rake
 ```

Or test a specific test:
```
rspec spec/models/assignment_spec.rb:57
```
