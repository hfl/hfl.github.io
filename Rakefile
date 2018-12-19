desc "Write a new blog"
namespace :blog do
	task :new, [:mytitle] do |t, args|
		mytime = Time.now
		post_title = args.mytitle||"My Title"
		file_title = post_title.downcase.split.join("-")
		post_file = "_posts/" + mytime.strftime("%Y-%m-%d") + "-" + file_title + ".markdown"
		File.open(post_file, "w+") do |f|
			f.puts "---"
			f.puts "layout: post"
			f.puts "title: \"#{post_title}\""
			f.puts "date: " + mytime.to_s
			f.puts "categories: Todo"
			f.puts "---"
			puts "Post has created."
		end
	end
end

