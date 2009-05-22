# Statistics

This ActiverRecord plugin allows you to easily define and pull statistics for AR models. This plugin was built with reporting in mind.

## Installation
script/plugin install git://github.com/acatighera/statistics.git

## Examples
Defining statistics is similar to defining named scopes:

class Account < ActiveRecord::Base
    define_statistic "Basic Count", :count => :all
    define_statistic "Basic Sum", :sum => :all, :column_name => 'amount'
end

Actually pulling the numbers is simple:

for all stats
Account.statistics

for a single stat
Account.get_stat(‘Basic Count’)

###Here are some additional benefits of using this plugin:

1. Easily Filter

Note: I found filtering to be an important part of reporting (ie. filtering by date). All filters are optional so even if you define them you don’t have to use them when pulling data. Using the filter_all_stats_on method and :joins options you can make things filterable by the same things which I found to be extremely useful.

class Account < ActiveRecord::Base
  define_statistic "Basic Count", :count => :all
  define_statistic "Custom Count", :count => :all, :filter_on => { :channel => 'channel = ?', :start_date => 'DATE(created_at) > ?'}
    filter_all_stats_on(:user_id, "user_id = ?")
end

Account.statistics(:user_id => 5)
Account.get_stat(‘Custom Count’,  :user_id => 5,  :start_date => ‘2009-01-01’)

2. Standardized

all_stats = []
[ Account, Post, Comment ].each do |ar|
  all_stats << ar.statistics
end

Account.get_stat(“Basic Count’)

3. Calculated statistics (DRY)

class Account < ActiveRecord::Base
    define_statistic "Basic Count", :count => :all
    define_statistic "Basic Sum", :sum => :all, :column_name => 'amount'
    define_calculated_statistic "Total Amount" do
      defined_stats('Basic Sum') * defined_stats('Basic Count')
    end
end

4. Re-use scopes you already have defined

class Account < ActiveRecord::Base
  named_scope :scope1, :conditions => “status = ‘active’”
  named_scope :scope2, :joins => :posts
  define_statistic "Chained Scope Count", :count => [:scope1, :scope2]
end

5. Accepts all ActiveRecord::Calculations options (:joins, :conditions, etc)

class Account < ActiveRecord::Base
  define_statistic "Accounts With Posts Count", :count => :all, :joins => :posts, :conditions => "status = 'active'"
end