#使用ROA方法设计参考资料功能

>目标：尝试设计RESTful的架构的服务

##1.ROA的设计流程

	1 Figure out the data set
	2 Split the data set into resources
	
	对于每一种资源：
	  
	3 Name the resources with URIs
	4 Expose a subset of the uniform interface
	5 Design the representation(s) accepted from the client
	6 Design the representations(s) served to the client
	7 Integrate this resource into existing resources, using hypermedia links and forms
	8 Consider the typical course of events: what's supposed to happen?
	9 Consider error conditions:what might go wrong?

##2. 过程记录

###2.1 找到数据集  
>1 Figure out the data set

每一个工作任务需要资源，资源使用两种方式来表现，一种是工作的对象，另一种是参考的资料。这里涉及参考资料的功能。

我们想提供以下的功能：

- 创建学习任务的人可以为它指定一些参考资料，这些参考资料对所有人可见
- 参与学习任务的人可以添加自己的参考资料
- 学习者可以为参考资料打分，并且能够通过打分进行一定的排序功能
- 每个参考资料是与一个学习任务相关的，可以就特定任务列出参考资料列表
- 每个参考资料是一个学习者添加的，可以为列出某个学习者所添加的所有的资料
- 每个参考资料与学习目标和关键词相关联，可以列出某个学习目标和关键词相关的参考资料列表

进行以上分析以后，需要使用的数据集是参考资料集。因为参考资料有各种表征形式，包括网页、文献、文件、图片、视频、音频、书籍等实体。这些是参考资料的属性。ROA方法的核心是找到资源，经过分析，实际上需要的资源不是参考资料，而是参考资料**列表**。尤其是按照task、user、learning objects、keywords这些词索引出来的参考资料**列表**，具体如下：

- 与某一个工作任务相关的参考资料**列表**
- 与某一个学习目标相关的参考资料**列表**
- 与某一个用户相关的参考资料**列表**
- 与某一个关键词相关的参考资料**列表**

###2.2 将数据集分类成资源
> 2 Split the data set into resources

本服务的核心资源就是参考资料，task、user、learning object、keywords这些资源用于辅助产生所需要的列表。参考资源的类型与www的资源类型一样丰富，暂时使用HTML列表来表述

###2.3 对资源进行命名
> 3 Name the resources with URIs

现在需要对资源进行命名。URI的命名包括几个原则，要具有描述性，每一个URI对应一个资源，具有可加性，同一个资源可以有几个不同的描述。在设计描述的时候，同时要考虑使用的情景，也就是使用者为什么要使用这些资源？

首先列举一些通常的好的URI类型：

静态获取：

http://www.example.com/relationships/Alice;Bob
http://www.example.com/software/release/1.0.3.tar.gz
http://www.example.com/software/release/latest.tar.gz
http://www.example.com/webblog/2006/10/24/0
http://www.example.com/map/roads/USA/AR/Little_Rock
http://www.example.com/wiki/Jellyfish
http://www.example.com/search/Jellyfish
http://www.example.com/nextprime/1024
http://www.example.com/next-5-primes/1024
http://www.example.com/sales/2004/Q4
http://www.example.com/bugs/by_state/open

可加性的获取：

http://www.google.com/search?q=jellyfish
https://mail.google.com/mail/?q=jellyfish&search=query&view=tl
http://www.google.com/search?q=jellyfish&start=10
http://www.example.co/forums?PHPSESSIONID=273839233109

考虑以上因素以后，资源的定位是这样的

学习任务的参考资料列表  task{#id}/refercences
学习目标相关的参考资料列表 BA123.1/references
用户上传的参考资料列表 users/:id/references tom/references
关键词的参考资料列表 keywords/references

以上的URI定位是在情景中使用，也可以有动态查询接口，用于查询特定的参考资料列表

查询某个学习任务的参考资料列表： /references/search?by=task&q=taskid
查询用户的参考资料列表： /references/search?by=user&q=username
查询关键词的参考资料列表: /references/search?by=keywords&q=learning
查询学习目标相关的资料列表： /references/search?by=learningobject&q=BA123.1

通过这样的设计，我们可以给所有的参考资料列表进行定位。并且，以上的设计满足描述性、可加性的原则

###2.4 公布统一接口的子集
> 4 Expose a subset of the uniform interface

未完成

###2.5 设计从客户端接收资源表现态
> 5 Design the representation(s) accepted from the client

在资源的功能中，包含添加新的参考资料，这些使用post的方式进行传递。另外包含使用json传递的方式


###2.6 设计传送给客户端的资源表现态
>6 Design the representations(s) served to the client

主要资源是一种列表，所以传递给客户端的资源表现态有几种，包括html的list、使用json表现的list、xml、csv等。主要使用两种方式，一种使用标准的html传递资源，一种使用json格式传递资源而后在客户端进行渲染

###2.7 设计超链接和表单，将当前资源与其他资源产生关联
>7 Integrate this resource into existing resources, using hypermedia links and forms

提供的资源包括以学习任务、学习目标、用户和关键词联系的资源列表。在静态使用情境中，需要添加form表单，使用户可以创建新的参考资料条目。在搜索情景中，通过搜索框和与task相关的推荐来添加不同列表之间的超链接。


###2.8
>8 Consider the typical course of events: what's supposed to happen?

ACTION分析：
当在学习任务情境中，学习者会查看参考资料列表，查看参考资料并点击获取。有时候会添加自己找到的参考资料。对于好的参考资料，还会点击有用标记以添加推荐数。

在搜索情景中，学习者会依据学习任务搜索参考资料，并且按照分类进行排序。点开参考资料进行查看。如果是查看自己的学习资料，学习者要能够看到自己的参考资料的使用情况，包括近期添加的参考资料，近期点击的参考资料，这些数据作为学习状态分析的一部分出现。

在搜索情景中，会点击推荐的链接以查看新的参考资料。

至于在服务器端发生的故事，有待具体研究

关于参考资料列表：

获取参考资料列表  GET /task{#id}/references /references/task{#id}
为参考资料列表添加一个新的条目  POST /references/task{#id}
参考资料列表删除一个条目  DELETE /references/task{#id}
修改参考资料列表中的条目信息 ？ POST /references/task{#id}/refid?

这部分需要迭代

###2.9
>9 Consider error conditions:what might go wrong?

未完成

##3. 开发
###3.1 生成资源
rails new greatref
rails g resource name:text uri:string type:integer task_id:integer user_id:integer learningobject_id:integer
rake db:seed

###3.2 设计routes

静态路由
	/tasks/task_id/references
	/users/user_id/references
	/learningobjects/learningobject_id/references

分别映射到references的不同action
	  get '/tasks/:task_id/references' => "references#by_task_id"
	  get '/users/:user_id/references' => "references#by_user_id"
	  get '/learningobject/:lo_id/references' => "references#by_lo_id"

动态路由：支持搜索

	/references/search?task_id=1&user_id=2
	get 'references/search(.:format)' => "references#search"

查询参数支持：
	def search
		#@refs = Reference.where(task_id: params[:id])  #按照搜索条件检索结果
		#render "search.html.erb"
		query_hash =  Rack::Utils.parse_nested_query(request.query_string)
		if query_hash.present? 
			#@refs = Reference.where(task_id: params[:task_id])
			@refs = Reference.where(query_hash) #需要添加错误处理
		elsif 
			@refs = nil
		end

	end

###3.3 设计表现态
要实现两种表现态，一种使用html的格式发送，另一种使用json的格式表现
使用bootstrap设计初步表格模板
设计post /tasks/:task_id/references => "references/create"动作
使用form_tag创建添加新参考资料的功能

为表格中的元素添加link，在不同的功能之间创建超链接，完成状态转移的功能

###3.4 json表现态设计

json的形式和json的解析


