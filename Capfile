# Load DSL and set up stages
require "capistrano/setup"

# Include default deployment tasks
require "capistrano/deploy"

require "capistrano/scm/git"
install_plugin Capistrano::SCM::Git


# load 'config/deploy'

# Load custom tasks from `lib/capistrano/tasks` if you have any defined
Dir.glob("lib/capistrano/tasks/*.rake").each { |r| import r }

set :mysql_params, "-u root"
set :mysql_db_name, "my_app"

after :deploy, :migrate
desc "migrate database on server"
task :migrate do
  on roles(:all)  do
    testcmd =  <<-EOS
      touch #{shared_path}/migration.list
      ls -1v #{current_path}/sql/*.sql 2>/dev/null > #{shared_path}/migration.available
      diff #{shared_path}/migration.available #{shared_path}/migration.list | awk \"/^</ {print \\$2}\" | while read f
      do echo \"migrating $(basename $f)\"; mysql #{fetch(:mysql_params)} #{fetch(:mysql_db_name)} < $f && echo $f >> #{shared_path}/migration.list ; done
      rm -f #{shared_path}/migration.available
    EOS
    execute testcmd
  end
end
