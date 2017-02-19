# chinese.misc
# 中文文本分析方便工具R包chinese.misc的中文说明
# 
# 本使用说明目前尚未完成，预计将于2017年4月以前完成。
# This Chinese manual has not been finished. It will be finished before Apr 2017.
# 
# ################
# 一、使用方法
# ################
# # 在Windows下的 R >=3.3.2 中，键入
# install.packages('chinese.misc')
# library(chinese.misc)
#
# ################
# 二、本R包的特点
# ################
# 编写本R包的目的主要是帮助使用者以相对自动和便捷的方式完成中文文本分析中一系列繁琐且易出错的工作；特别是帮助对文本分析的流程不太了解的初学者。
# 特点如下：
#  （1）能够仅用一行代码生成文档-词项矩阵，并在此过程中完成自动检测编码并读取文件、中文文本分词、定制性的文本清理、去除停用词等工作。
# （2）常用的tm包在用于中文文本时会有些恼人的bug；而chinese.misc包为此进行了一些改进，因此更适合处理中文。
# （3）通过一些方法宽容用户的不正确输入，尝试纠正错误，或在报错时给出一般人能读得懂的报错信息。频频报错飘红什么的实在是太心烦了；错误少，大家心情都舒畅。
# （4）提供了若干在中文文本分析以外的任务中也能使用的函数，如类型判断、类型转化。
#
# ################
# 三、函数概览
# ################
# （一）用于自动读取中文文本、分词、生成语料或文档-词项矩阵：
# corp_or_dtm
# scancn
# seg_file
# dir_or_file
#
# （二）用于去除停用词或无意义词语：
# make_stoplist
# slim_text
#
# （三）用于txt/rtf和csv互转，方便那些偏好某种格式文件的童鞋：
# csv2txt
# txt2csv
#
# （四）类型判断、类型转化
# is_character_vector
# is_positive_integer
# as.character2
# as.numeric2
#
# （五）查看、统计词频
# output_dtm
# sort_tf
#
# （六）其它函数
# tf2doc
# m2doc
# match_pattern
#
# ————以下将会分别介绍这些函数————
#
# ################
# 四、函数介绍
# ################
#
# 为进行讲解，首先需要提供几段文本做例子。
# 复制并运行以下代码（不用修改），会在你的工作目录里生成名为hehe的文件夹。如果这个文件夹之前已经在你电脑中存在了，会报错，请你自己改一个别的名。
# 程序会自动在这个文件夹里写入几个文件当样例。
#
# ###   ^_^ 从这行开始复制
#
# a_new='hehe' #If you want to change folder name, just change this.
# gwd=getwd()
# f=paste(gsub('/', '', gwd), a_new, sep='/')
# if (dir.exists(f)) stop ('Folder already exists. Please change a name.')
# dir.create(f)
# dir.create(paste(f, 'f1', sep='/'))
# dir.create(paste(f, 'f2', sep='/'))
# x='以事件为选题的数据新闻最常出现在重大新闻事件的报道中。在这类事件中，数据报道可能是媒体精心制作的报道主体，也可能是媒体对事件的整个专题报道中的一个有机组成部分。可预见的重大新闻事件一般多指会议、活动、庆典或赛事，媒体可以把较为充足的时间投入到选题策划中。除了可预见的重大新闻事件以外，更多此类数据新闻的选题是突发新闻事件。近年来，越来越多的媒体将数据新闻运用于突发新闻事件的报道中，大量数据资源的整合和运用为此类新闻报道增添了更多科学性。'
# write.table(x, paste(f, 'f1/d1.txt', sep='/'), row.names=FALSE, col.names=FALSE, quote=FALSE, fileEncoding='UTF-8')
# x='人们对数据可视化的适用范围有着不同观点。例如，有专家认为数据可视化是可视化的一个子类目，主要处理统计图形、抽象的地理信息或概念型的空间数据。现代的主流观点将数据可视化看成传统的科学可视化和信息可视化的泛称，即处理对象可以是任意数据类型、任意数据特性，以及异构异质数据的组合。大数据时代的数据复杂性更高，如数据的流模式获取、非结构化、语义的多重性等。'
# write.table(x, paste(f, 'f1/d2.txt', sep='/'), row.names=FALSE, col.names=FALSE, quote=FALSE, fileEncoding='UTF-8')
# x='政治传播学是政治学与传播学的交叉学科，它是对政治传播现象的总结和政治传播规律的探索和运用，它包括政治传播的结构、功能、本质及技巧等方方面面。它的研究范围包括：政治传播行为，即政治传播的主体、客体及他们之间的相互关系体系；政治传播内容，即对政治的信息处理体系；政治传播途径，即政治符号和传播媒介体系；政治传播环境，即政治传播与相关社会现象；政治传播形态，即政治传播本体的形貌或表现体系。'
# write.table(x, paste(f, 'f2/d3.txt', sep='/'), row.names=FALSE, col.names=FALSE, quote=FALSE, fileEncoding='GB18030')
# x='改进社会治理方式。坚持系统治理，加强党委领导，发挥政府主导作用，鼓励和支持社会各方面参与，实现政府治理和社会自我调节、居民自治良性互动。坚持依法治理，加强法治保障，运用法治思维和法治方式化解社会矛盾。坚持综合治理，强化道德约束，规范社会行为，调节利益关系，协调社会关系，解决社会问题。坚持源头治理，标本兼治、重在治本，以网格化管理、社会化服务为方向，健全基层综合服务管理平台，及时反映和协调人民群众各方面各层次利益诉求。'
# write.table(x, paste(f, 'f2/d4.txt', sep='/'), row.names=FALSE, col.names=FALSE, quote=FALSE, fileEncoding='GB18030')
# x='所有这三种活动和它们的相应境况都与人存在的最一般状况相关：出生和死亡，诞生性和有死性。劳动不仅确保了个体生存，而且保证了类生命的延续。工作和它的产物——人造物品，为有死者生活的空虚无益和人寿的短促易逝赋予了一种持久长存的尺度。而行动，就它致力于政治体的创建和维护而言，为记忆，即为历史创造了条件。'
# write.table(x, paste(f, 'd5.txt', sep='/'), row.names=FALSE, col.names=FALSE, quote=FALSE, fileEncoding='UTF-8')
#
# ###   ^_^ 复制到这一行就OK啦
#
# （一）收集文件名、读文件
#  
# ######      dir_or_file
#
# #现在你已经有了一个装了五个文件的文件夹f。
# #用dir可以收集这个文件里的文件名
# dir(f)
# #然而，dir只能下到文件夹的一层，怎样才能读到嵌套在子文件夹里的文件呢，这时候就要用chinese.misc包里的dir_or_file了。
# 这个函数允许你同时输入多个文件夹或文件的名称，两者混着往里放也没事；这些放进去的名字，既可以是绝对路径，也可以是相对路径，也可能是以~开头的路径。
# 无论如何，这个函数会判断文件是否存在（如果不存在则会报错），去掉重复的文件，然后把从小到大放了序的全部文件名收集起来。
# 假如你要分析的文件在不同的文件夹里，甚至有的在C盘，有的在F盘，有的在工作目录里，有的不在，有的是文件夹的名称，有的是文件的名称，那么就可以用这个函式来整理和收集你的文件名了。
# 
# dir_or_file (
#     ...,  #一个或多个代表文件夹/文件名
#     special = "" #代表模式的正则表达式或字符
# ) 
#
# 其中，...最好是字符或字符向量，但如果是一个由文件名组成的矩阵、列表什么的，也OK，反正程序会尝试进行转化。
# special代表你想要的文件的模式，默认是收集所有文件，但如果你只想要以txt结果的文件，就设special='txt$'。
#
# #举个栗子
# all_file=dir_or_file(f)
# all_file
# #看看是不是收集到了所有文件
# #如果你的文件名有重复，也没事，反正最后会去重的
# all_file=dir_or_file(f, f)
#
# ######      scancn
#
# 好的，现在我们已经收集好了文件名，接下来我们要读它们了。
# 我们要用chinese.misc包中名为scancn的函数。
# 它实际上就是一个scan函数，但是它可以自动检测文件编码，减少乱码的可能性，判断文件是否有实质内容，去除一些非法字符，并最后把文件拼接成单一字符。
# 这个函数永远不会输出0长度的对象或NA。因此，如果你把它读出来的结果传递给其它函数，就是相对安全的；如果你的文本里边只有空格或标点，也会被视为无实质内容。当文本无实质内容时，程序会继续运行，不会报错，但会弹出提示。
# 当然，你用这个函数读英文文本也行，只是英文文本大多不会乱码，所以不需要特意用这个函数。
# 
# scancn(
#     x, #一个文件名
#     enc = "auto", #编码，默认为自动
#     read_2nd = TRUE #是否尝试读取两次
# )
# 
# 其中，enc是你的文件的编码。如果你的很多文件有不同的编码，或者你不知道编码，或者你已经被乱码搞得头大了，就不用改了，让它自动检测吧。如果你确认你的文件就是某种编码，那就直接设一个值就行了，可省下几微秒的检测时间。
# read_2nd询问的是，是否要对被视为UTF-8文件但实际上并不是且无法正常读取的文件进行二次读取。默认为TRUE，基本上不用您改。
#
# 写个循环，依次读取五个文件，看能不能读出来。
# for (each in all_file){
#     message(each)
#     text=scancn(each)
#     print(text)
# }
# #都打印在你的屏幕上了吧！实际上第一、第二个文件都是UTF-8编码，第三、四个文件是GB18030编码，而scancn会主动对编码进行检测。
#
# ######      seg_file
# 中文文本分析的一个突出特点就是需要分词。当然，分词也很简单，用jiebaR包先设个分词器，再segment( )就行了。但是，我们可以用chinese.misc包里的seg_file来做的，这个包也是调用jiebaR来分词，但是更适合懒人使用。偷懒不需要借口！！！
#
# seg_file(
#     ..., #一个或多个文件/文件夹名
#     from = "dir",  #你的输入同文件/文件夹名，还是直接就是要处理的文本
#     folder = NULL,  #写入分词结果的文件夹
#     mycutter = DEFAULT_cutter,  #分词器
#     enc = "auto",  # 编码
#     myfun1 = NULL,  #定制函数1
#     myfun2 = NULL,  #定制函数2
#     special = "",  #文件名需符合何种模式
#     ext = "txt" #若写入分词结果，以什么为后缀
# )
#
# 看着参数有点儿多是吧。没事，复制粘贴一下上述代码就行了，有用的参数就改一下，不用改或不会改的放着不管就行了。其实真正需要你自己设的也没多少。
# 其中，...是字符向量，这个必须是字符向量，而不能像dir_or_file那样可以随便放什么东西。
# from是要告诉程序你这些字符是文件或文件夹的名子呢，还是说这些字符本身就是你要分词的文本。默认为'dir'，也就是文件/文件夹名，如果输入本身就是文本，务必要改成from='v'，否则程序会把输入当文件名处理，然后显然就要报错了。
# 如果你要把分词的结果写到硬盘上，请给folder一个有效的文件夹名。这个文件夹可能已经存在，但也可能不存在，如果不存在的话，程序会尝试创建一个，而且可以创建多层文件夹，但也可能创建失败。如果你不想写到硬盘上，只要设成NULL就行了。当然，默认值就是NULL，不用麻烦您设了。
# my_cutter是函数使用的分词器，请加载jiebaR包生成。如果你不设它的话，也会使用一个预先存在的默认分词器，这个分词器其实就是DEFAULT_cutter=worker(write=FALSE)。所以如果你不是想为分词器添加新词的话，这一项也不用麻烦您设了。
# enc是编码，你可以指定一个文件编码，但也可以让它默设为'auto'，即自动检测。
# myfun1的值应该是一个函数，这个函数将会对被scancn读取出来，但尚未被分词的文本进行处理。默认是NULL，也就是不做处理。
# myfun2也需指向一个函数，只是这个函数将会被用来对分词之后的文本进行处理。当然你要这个定制函数的结果是一个长度为1的、词与词之间用空格相连的字符向量；如果不是字符向量的话，程序会尝试进行转换。
# special在读取文件时（也就是当from='dir'）时，只有符合这个模式的文件才会被读
# ext指的是，如果你要把分词的结果写到硬盘上，文件要什么后缀名。这个只能设成"txt"、 "rtf"或""。否则，会报错。
#
# 看几个栗子，用来证明真心不需要您亲自设定很多参数。
# y=seg_file(all_file) #输入是文件，但最后结果不写入硬盘
# seg_file(all_file, folder='hehe/seg') #输入是文件，最后写进硬盘，同时在hehe文件夹里新创新了一个文件夹。
# all_text=unlist(lapply(all_file, scancn))
# y=seg_file(all_text, from='v') #all_text已是是待处理的文本向量了，而不是文件名。最后不写入。
# seg_file(all_text, from='v', folder='hehe/seg2') #直接处理文本，最后写入文件夹，同时创新一个seg2文件夹。
#
# ######      corp_or_dtm
#
# 不是说好了一步出文档-词语矩阵么，为啥前边讲什么收集文本、分词之类的？好吧，现在该轮到讲这个一步生成的函数了。灯灯灯等~~
# 
# corp_or_dtm(
#     ...,  #一个或多个文件/文件夹名，或待处理的文本
#     from = "dir", #你的输入是文件/文件夹名，还是待处理的文本
#     type = "corpus", 
#     enc = "auto", 
#     mycutter = DEFAULT_cutter, 
#     stop_word = NULL, 
#     stop_pattern = NULL, 
#     control = DEFAULT_control1, 
#     myfun1 = NULL, 
#     myfun2 = NULL, 
#     special = ""
# ) 
#
# ...必须是多个字符，不能像dir_or_file里那样可以是各种对象，因为在corp_or_dtm里不会进行强制转换。（主要是考虑到程序跑的速度！！！）
# from是要告诉程序你这些字符是文件或文件夹的名子呢，还是说这些字符本身就是你要分词的文本。默认为'dir'，也就是文件/文件夹名，如果输入本身就是文本，务必要改成from='v'，否则程序会把输入当文件名处理，然后显然就要报错了。
# type，你最后想让程序输出什么？设成c, corp, corpus不用区分大小小，表明你要的是输出Corpus对象；如果是dtm、DTM，就是文档-词项矩阵；如果是tdm、TDM，则是词语-文档矩阵；如果是其它的值，也会生成Corpus。默认是生成Corpus。当然，我猜您最想要的是dtm吧，还麻烦您手动改一下。
# my_cutter与在seg_file中不同，现在它既可能是函数使用的分词器，也可能是NULL。请加载jiebaR包生成。如果你不设它的话，也会使用一个预先存在的默认分词器。如果你不是想为分词器添加新词的话，这一项也不用麻烦您设了。如果是NULL的话，就是不进行分词，这适用于你的文本已经分过词而不用再分词的情况。
# enc是编码，你可以指定一个文件编码，但也可以让它默认为'auto'，即自动检测。
# stop_word需设定一个字符向量作为停用词。默设为NULL，即不去除。当然，大家可能比较懒，又想去除停用词，又懒得去找停用词表，就算有也懒得加载。嗯，好的，没事，只要设stop_word='jiebar'就行！！！这样就直接使用jiebaR的停用词了。
# stop_pattern可能光去除停用词还不够，你还想去除具有某种模式的所有词，那么可在这里设个正则表达式向量。默认是NULL，多数情况上不用管，反正大家基本上也不用这种方法去除停用词。
# control是传递给DocumentTermMatrix或TermDocumentMatrix的参数。你可以自己设，不过不设也行，反正会指向一个默认值DEFAULT_control1，所以这个参数也不用您管了。DEFAULT_control1是自动生成的，你把control设成NULL，也仍然会指向它；不过你也可以改成DEFAULT_control2，两者的区别是，前者允许的词语最小长度是1，后者允许的最小长度是2。两者设定的词语最大长度都是25。
# myfun1的值应该是一个函数，这个函数将会对被scancn读取出来，但尚未被分词的文本进行处理。默认是NULL，也就是不做处理。
# myfun2也需指向一个函数，只是这个函数将会被用来对分词之后的文本进行处理。当然你要这个定制函数的结果是一个长度为1的、词与词之间用空格相连的字符向量；如果不是字符向量的话，程序会尝试进行转换。
# special在读取文件时（也就是当from='dir'）时，只有符合这个模式的文件才会被读
#
# 好的，还是举栗子吧。需要您动手设的参数真的很少。
# 程序运行过程会，会弹出提示告诉你机器现在做什么。
# dtm=corp_or_dtm(all_file, type='dtm', stop_word='jiebar')
# mycorp=corp_or_dtm(all_text, from='v', stop_word=c('嗯嗯', '呵呵', '去洗澡'))
# dtm=corp_or_dtm(all_text, from='v', type='dtm', control=DEFAULT_control2)
#
# （二）去除停用词
# ######      make_stoplist
#
# make_stoplist(x='jiebar') 
#
# 你是不是觉得还得去找停用词表太麻烦，好的，没事，用make_stoplist('jiebar')或直接make_stoplist( )什么都不加，就可以直接调用jiebaR的停用词了。
# 如果你想用自己的停用词文件，就输入x，即文件名，就可以读取了。理想情况是你有个TXT文件，其中每个词语占一行。但事实上，可能你的停用词表没那么规范，比如：
# ------机制，大力、加强,努力, 全面6确保7 完成/实现|实现\n
# ------全党 NA 不断\t
# ------全国\人民
# ------促进999持续
# ------1不要2更要3既要4六要5
# 那怎么办呢？没事，make_stoplist会尽量帮你整理成一个干净的停用词表。完成后，会把最靠前的几个词打印在屏幕上帮你确认读取成功。
# 如果你的文件里有重复的词也没事，因为会自动去重。
# 至于文件的编码，可自动检测。
#
# ######      slim_text
#
# 用停用词表时，会发现为了去掉更多无实质意义的词，你不得不弄一个很长的停用词表，在你想到某个词并把它加到表里之前，你就没法去除它。那怎么办呢？没事，我们通过给词语标注词性来把无意义的词去掉。
#
# slim_text(
# 	x, #字符
# 	mycutter = DEFAULT_cutter, 分词器
# 	rm_place = TRUE, #是否删去跟地点相关的，默认为是
# 	rm_time = TRUE,  #是否删去跟时间相关的，默认为是
# 	rm_eng = FALSE,  #是否删去含英文，默认为否
# 	paste = TRUE #是否把结果拼接成一个字符，默认为是
# )
#
# 这样就能达到大量削减词语数的目的了。
#
# x='所有这三种活动和它们的相应境况都与人存在的最一般状况相关：出生和死亡，诞生性和有死性。劳动不仅确保了个体生存，而且保证了类生命的延续。工作和它的产物——人造物品，为有死者生活的空虚无益和人寿的短促易逝赋予了一种持久长存的尺度。而行动，就它致力于政治体的创建和维护而言，为记忆，即为历史创造了条件。'
# #先看直接分词的效果
# seg_file(x, from='v')
# #再看用词性来删词的效果
# slim_text(x)
# # 词语数大约少了少分之一



