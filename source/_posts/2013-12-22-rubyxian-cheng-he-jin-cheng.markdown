---
layout: post
title: "Ruby线程和进程"
date: 2013-12-22 00:04:56 +0800
comments: true
categories: Ruby 线程 进程
---

###线程的创建
```
thead = Thread.new("www.google.com") do |url|
  Thead.current[:count] = 1;
  http = Net::HTTP.new(url, 80)
  print "Fetching: #{url}\n"
  res = http.get('/')
  print "Got #{url}: #{res.message}\n"
  "hi"
end
thead.join
p thead[:count]
p thead.value
```
###线程的方法
```
Thead#jion #主线程等待
Thead#value #等待返回Thead的最后一个代码的返回值
Thread#status
Thread#alive?
Thread#priority=
Thead#current
```
###线程变量
```
Thead.current[:count] = 1;
```
###线程异常
如果没有调用thead.jo'in则线程里面的异常不会引起主线程的崩溃，如果调用join，可以通过设置类变量来避免
```
Thead.abort_on_exception = false
```
###线程加锁
```
mutex = Mutex.new
threads = 10.times.map do
  Thread.new do 100_000.times do
  mutex.lock

  mutex.unlock 
end 
```
或者
```
mutex = Mutex.new
threads = 10.times.map do
Thread.new do 100_000.times do
  mutex.synchronize do 
    
  end
end 
```
#####如果想取得锁但是又不想暂停当前线程调用try_lock,如果想暂时解锁锁，可以调用sleep
```
rate_mutex = Mutex.new
exchange_rates = ExchangeRates.new
exchange_rates.update_from_online_feed
  Thread.new do rate_mutex.lock 
  loop do
    rate_mutex.sleep 3600
    exchange_rates.update_from_online_feed
  end 
end
loop do
  print "Enter currency code and amount: " 
  line = gets
  if rate_mutex.try_lock
    puts(exchange_rates.convert(line)) ensure rate_mutex.unlock 
  else
    puts "Sorry, rates being updated. Try again in a minute" 
  end
end
```
###创建进程
这两个方法是同步的，除了这两个，其它的都是不同步的，主进程可以先退出，而子进程不会被主进程退出
```
system("tar xzf test.tgz") # => true 只返回成功与否
`date` # => "Mon May 27 12:31:17 CDT 2013\n"
$? #返回最后一个进程的返回值
```
#####如果需要输入、输出。这些不是同步
```
pig = IO.popen("local/util/pig", "w+") pig.puts "ice cream after they go to bed" pig.close_write #发送end-of-file给pig的输入
puts pig.gets

################## 我是分割线

IO.popen("date") {|f| puts "Date is #{f.gets}" }

################## 我是分割线

fork do
  puts "In child, pid = #$$" 
  exit 99
end
pid = Process.wait
puts "Child terminated, pid = #{pid}, status = #{$?.exitstatus}"
#produces:
#In child, pid = 22033
#Child terminated, pid = 22033, status = 99
```
###子进程交互,只在支持fork的操作系统上有用，windows不支持
```
pipe = IO.popen("-","w+")  #进程的第一个参数必须为 "-" minus sign
if pipe
  pipe.puts "Get a job!"
  STDERR.puts "Child says '#{pipe.gets.chomp}'" 
else
  这里是子进程执行的
  STDERR.puts "Dad says '#{gets.chomp}'"
  puts "OK" 
end
```
###独立子进程
```
exec("sort testfile > output.txt") if fork.nil? 
# exec在子进程执行，因为fork.nil在子进程中为真
Process.wait

############# 我是分割线

trap("CLD") do
pid = Process.wait
puts "Child pid #{pid}: terminated"
end
fork { exec("sort testfile > output.txt") }

#produces:
#Child pid 123456: terminated

############# 我是分割线

#这个表明子进程不会退出
fork do
  5.times do
    sleep 1
    puts "I'm an orphan!"
  end
end

abort "Parent process died..."
```