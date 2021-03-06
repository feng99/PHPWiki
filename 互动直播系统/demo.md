

回声消除问题



信令控制，登录、鉴权、权限管理、状态管理等等，各种应用服务，消息推送，聊天，礼物系统，支付系统，运营支持系统，统计系统等。

后台还有数据库，缓存，分布式文件存储，消息队列，运维系统等。



![img](https://user-gold-cdn.xitu.io/2017/12/8/160362bd7d1bc09c?imageslim)



●PC、Android、 ios三大平台
●每个平台要做2种端:面向**客户的直播端****,和面向**主播的推流端**(标配x2)
●视频编码涉及非常多的技术参数和细节(领域特殊技术)
●完整的礼物系统、支付系统、运营系统、任务系统(不亚于-般页游项目)
●即时聊天IM服务(弹幕,既要保证实时性,又要抗住高并发)
●视频推流拉流链路依赖第三方CDN (超越一 般创业项目的运维成本)
●因为涉及钱的问题，经常与各种黑暗势力斗争(色情、广告、刷小号、刷充值、告侵权、DDoS
等等)

●支付系统一定要仔细搞啊，每个环节都要仔细想清楚**各种异常情况**啊，**所有消费都要有流水记**
**录**，警惕苹果黑卡啊



**2.在线转码：**推流端其实做了很多把更好的画质想尽办法传上来的工作，投了很多人力来做。传上来之后，观看也在移动端，它不一定看得了。如果他看不了怎么办？我们就需要在线转，在线转码其实承担的更多更重要的事情。

**3.鉴权：**用户都不想被盗链，尤其是推流的时候，如果我不鉴权，谁都可以来推。所以这是必须要有的

**4.内容审核：**现在我们没有办法帮他做到自动审核，技术还不够。现在做到的是截图，按用户指定的时间定期截图，这样的话，用户就可以请一些外包来看是不是有敏感内容，是不是要下线，这个对于现在这种三四秒延迟的直播来说非常重要。你做不到的话，没准政策因素你就做不下去了。



------

 



**如何快速搭建一个完整的手机直播系统**

在这个直播如火如荼的时代，各大云服务提供商也站到了时代的风口上，因此，如何选择产品和服务快速搭建直播系统，我想应该是众多创业者最关心的问题了，趣拍直播SDK就很好集成，下面会跟大家一一分享。

下面，我们先看下搭建一个完整的手机直播都包含哪些必须的环节：推流端（采集、前处理、编码、推流），服务端处理（转码、录制、截图、鉴黄），播放器（拉流、解码、渲染）、互动系统（聊天室、礼物系统、赞）。 

**手机直播推流端需要做哪些工作？**

直播推流端即主播端，主要通过手机摄像头采集视频数据和麦克风采集音频数据，经过一系列前处理、编码、封装，然后推流到CDN进行分发。

**采集**

手机直播SDK通过手机摄像头和麦克风直接采集视频数据和音频数据。其中,视频采样数据一般采用RGB或YUV格式、音频采样数据一般采用PCM格式。对于采集到的原始音视频的体积是非常大的，因此需要经过压缩技术来处理，降低视频的大小来提示传输效率。 在手机视频采集方面，iOS系统在硬件的兼容性方面做得比较好，系统本身提供了比较完整的视频采集的接口，使用起来也比较简单。但是，Android系统就比较麻烦了，千奇百怪的机型都有，适配起来非常难。我们在初期做了一项调研，发现Android的适配率还不到50%。

**前处理**

在这个环节主要处理美颜、水印、模糊等效果。特别是美颜功能几乎是直播的标配功能，没有美颜的直播主播们根本提不起兴趣。我们见过太多case是因为没有美颜功能被抛弃使用的。另外国家明确提出了，所有直播都必须打有水印并回放留存15天以上。所以，在选择直播SDK时，没有美颜和水印功能基本就可以选择放弃了。

美颜实际上是通过算法去识别图像中的皮肤部分，再对皮肤区域进行色值调整。通常情况下人的肤色与周边环境色调存在较大差异，通过颜色对比，找到皮肤的基本轮廓，进一步进行肤色检查还可以确定人脸范围。找到了皮肤的区域，可以进行色值调整、添加白色图层或调整透明度等来等来达到美白效果。美颜除了美白效果还需要磨皮功能，磨皮实际上就是用模糊滤镜实现的。滤镜有很多种，如高斯滤波，双边滤波，导向滤波，到底选择什么样的模糊滤镜各家也有自己的喜好。

在美颜处理方面，最著名的GPUImage提供了丰富的效果，同时可以支持IOS和Android，还支持自己写算法实现自己最理性的效果。GPUImage本事内置了120多种常见滤镜效果，添加滤镜只需要简单调用几行代码就可以了，比如大家可以试试使用GPUImageBilateralFiter的双边滤波滤镜来处理基本的磨皮效果，想要实现更理想的效果还是要通过自定义算法去实现的，各家也都有自己一套算法。

**编码**

为了便于手机视频的推流、拉流以及存储，通常采用视频编码压缩技术来减少视频的体积。现在比较常用的视频编码是H.264，但具有更高性能的H.265编码技术正在飞速发展，并可能很快成为主流；在音频方面，通比较常用的是用AAC编码格式进行压缩，其它如MP3、WMA也是可选方案。视频经过编码压缩大大提高了视频的存储和传输效率，当然，经过压缩后的视频在播放时必须进行解码。通俗点讲就是编码器将多张图像进行编码后产生一段段GOP（Group of Pictures），播放时解码器读取一段段GOP进行解码后读取图像并进行渲染显示。 在编码方面的核心是在分辨率、码率、帧率等参数中找到最佳平衡点，达到体积最小画面最优的效果，这些参数各家也都有自己的一套核心参数。

2012年8月，爱立信公司推出了首款H.265编解码器，六个月后，国际电联(ITU)就正式批准通过了HEVC/H.265标准，称之为高效视频编码(High Efficiency Video Coding)，相较于之前的H.264标准有了相当大的改善，做到了仅需要原来一半带宽即可播放相同质量的视频，低于1.5Mbps的网络也能传输1080p的高清视频。国内，如阿里云、金山云都在推自己的H.265编解码技术，随着直播的快速发展和对带宽的依赖，H.265编解码技术已有全面取代H.264的趋势。当然，全面推开应用还需要些时间。
另外，硬件编码已经成为手机直播的首选方案，软编码处理在720p以上的视频颓势非常明显。在IOS平台上硬件编码的兼容性比较好，可以直接采用，但在 Android 平台上，Android的MediaCodec 编码器，针对不同的芯片平台表现差异还是非常大的，要完全实现全平台兼容的成本还是非常高的。 

**推流**

要想用于推流还必须把音视频数据使用传输协议进行封装，变成流数据。常用的流传输协议有RTSP、RTMP、HLS等，使用RTMP传输的延时通常在1–3秒，对于手机直播这种实时性要求非常高的场景，RTMP也成为手机直播中最常用的流传输协议。最后通过一定的Qos算法将音视频流数据推送到网络断，通过CDN进行分发。 在直播场景中，网络不稳定是非常常见的，这时就需要Qos来保证网络不稳情况下的用户观看直播的体验，通常是通过主播端和播放端设置缓存，让码率均匀。另外，针对实时变化的网络状况，动态码率和帧率也是最常用的策略。

当然，在网络传输方面全部自己来做基本不现实，找提供推流服务的CDN服务商提供解决方案是最好的选择，可参考文章开头介绍的云视频服务商。据了解，阿里云是国内唯一能自研CDN缓存服务器的厂商，性能还是非常有保障的。通常，大多数直播平台都会同时接入多个视频云服务提供商，这样可以做拉流线路互备，对推流后视频集群再进行优化也可提高直播的流畅性和稳定性。

**服务端处理需要做哪些工作？**

要想适配各终端和平台，服务端还需要对流进行转码，如支持RTMP、HLS、FLV等格式拉流，支持一路转多路适配不同网络和分辨率的终端设备。另外，像现在必备的鉴黄功能也需要服务端完成。

**截图、录制、水印**

像阿里云、金山云、UCloud等云服务商都提供了实时转码技术将用户推流码率较高（比如720P）实时转化成较低清晰度（比如360P）的流以适应播放端的需求。如果要自己搭建实时转码系统，这个成本是极高的。一台8核设备只能实时转10 路流，如果一个正常的直播平台有1000路流，那至少就需要100台设备，加上后期的运维成本，一般公司就吃不消了。实时截图功能和实时转码类似，只是一般单机可以处理100路流。市面上云服务提供商基本上都提供了服务端转码、截图、录制功能，建议选择好的云服务提供商即可，可以节约大量成本。

**鉴黄**

2016年，4月14日上午10时，文化部公布了一则消息，斗鱼、虎牙、YY、熊猫TV、战旗TV、龙珠直播、六间房、9158等网络直播平台因涉嫌提供含宣扬淫秽、暴力、教唆犯罪等内容的互联网文化产品，被列入查处名单。文化部已部署相关执法机构查处涉案企业，将及时公布处罚结果。在前期的野蛮生长后，国家介入管制一定程度上遏制了直播的发展速度，但更有利于直播行业打造健康的生态，进入良性发展。这也意味着直播行业鉴黄成了必须环节，使用技术手段去鉴黄是手机直播平台必然采用的方案。
市面上提供鉴黄服务的方案主要有两种，第一种是对视频进行截图，然后对图片进行鉴黄，返回鉴黄结果和分值。典型的企业有阿里（绿网）、图谱科技，他们目前都支持直接传入视频，经过服务端分析返回结果，鉴黄的结果分为色情、疑似色情、正常或性感，并对每种结果进行打分。通常由业务系统接入鉴黄服务，根据鉴黄结果对直播流进行控制，如切断直播流、禁用用户的账号等。第二种是和CDN结合，直接对直播流进行分析，识别结果也分为色情、疑似色情、性感和正常，业务系统根据识别结果直接控制直播流。典型的企业是Viscovery，这套方案的优点是实时性保证比较好，缺点是必须部署到CDN或自己的机房，使用成本相对高一些。

趣拍微视频云服务作为一站式直播解决方案提供商，我们的主旨是让用户以最短时间、最小成本接入直播服务。因此，用户只需在控制台对鉴黄服务进行配置就可以针对每个应用，每一路直播流进行实时审核，审核内容包括色情、暴恐、时政敏感等。在控制台中，趣拍微视频服务实时将鉴黄结果返回，用户可以直接查看色情直播和违规界面的截图，同时可以对直播流进行控制，切断问题直播流。我们提供了短信、邮件和站内信提供功能，避免漏洞一个非法视频，给平台造成损失。数据统计功能让用户可以把握平台最新的动态信息，为进一步采取必要的措施提供可靠的依据。同时，为了满足用户定制化需求，我们还提供了丰富的接口，可以很方便的将鉴黄服务接入到自己的业务系统。

**播放器端需要做哪些工作？**

在播放器端如何做到秒开，在直播过程中保证画面和声音清晰度的同时，稳定、流程、无卡顿的直播流量，这些工作都需要播放器端配合服务端来做优化，做到精确调度。

**拉流**

拉流实际是推流的逆过程。首先通过播放端获取码流，标准的拉流格式有RTMP、HLS、FLV等。RTMP是Adobe的专利协议，开源软件和开源库都支持的比较好，如开源的librtmp库，播放端只要支持flashPlayer的就能非常简单的播放RTMP直播，直播延迟一般在1–3秒。HLS是苹果提出的基于HTTP的流媒体传输协议，HTML5可以直接打开播放，通过微信、QQ等软件分享出去，用户也可以直接观看直播，可以说手机直播app，HLS拉流协议是必须支持的，缺点是延迟通常大于10秒。FLV（HTTP-FLV）协议是使用HTTP协议传输流媒体内容的一个协议，也不用担心被Adobe的专利绑架，直播延迟同样可以做到1–3秒。

趣拍微视频云服务的直播拉流提供了RTMP、HLS、FLV三种格式，满足不同业务场景的需求，如对即时性要求较高或有互动需求的可以采用RTMP或FLV格式进行直播拉流播放；对于有回放或跨平台需求的，推荐使用HLS。当然，三种协议是可以同时使用的，分别用到自己的场景就可以了。 

**解码和渲染**

拉流获取封装的视频数据后，必须通过解码器解码、渲染后才能在播放器上播放。它是编码的逆过程，是指从音视频的数据中提取原始数据。前面介绍的H.264和H.265编码格式都是有损压缩，所以在提取后的原始数据，并非原始采样数据，存在一定的信息丢失。因此，在视频体积最小的情况下通过各种编码参数保留最好的原始画面，成为了各视频公司的核心机密。

考虑对高清的支持，解码肯定还是要选择硬解码的。前面介绍过，IOS系统由于硬件比较单一、比较封闭，支持的比较好，Android系统由于平台差异非常大，编解码要完全兼容各平台还需要很多工作要做。

渲染最大的难点不在与画面绘制，而在于画音同步，业内大部分直播平台在这块做的都还是不够的。我们在这方面积累了一些经验和大家分享。 

**手机直播中的交互系统**

手机直播中最常见的交互有聊天室（弹幕）、点赞、打赏和礼物等，有些比较有特色的手机直播平台也加入了和主播互动的游戏功能。交互系统涉及消息的实时性和互动性，在技术实现上大多是使用IM的功能来实现的，对服务器的压力也是比较大。 对于在线人数比较多的房间，弹幕消息量是非常大，主播与用户其实都看不过来，为了缓解服务器压力，在产品策略可以做一些必要的优化，比如对于发送消息的频率进行限制或对每条消息发送对象的上限进行限制等。

**聊天室**

手机直播中的弹幕交互功能已经成为直播必不可少的部分，是用户和主播互动的主要方式。手机直播中的弹幕实际上就是IM中的聊天室功能。聊天室和群聊功能类似，但聊天室的消息是不需要分发给不在线的用户的，对于历史消息也不需要查看，用户只有进入聊天室后才能查看聊天消息和群成员信息。要面对复杂多变的网络状况，还需要根据用户位置就近选择近对应运营商的单线机房接入弹幕消息服务，让弹幕更及时。当然，可以根据团队情况选择自己搭建还是选择第三方的聊天服务。 

趣拍直播SDK提供丰富的聊天室功能和接口，以最简单的方式对接自己的聊天系统或第三方的聊天系统。 

**礼物系统**

礼物系统已是绝大多数手机直播平台的标配了，它是这些平台主要的收入来源。在手机直播平台上我们常常可以见到土豪秒榜、土豪对刷的情景，据报道，明星直播一场礼物收入几十万也是常有的事，一年千万收入的网红也不少，可见国内有礼物消费习惯的土豪还不少。另一方面，送礼物的形式增强了用户和主播之间的互动交流，也是主播依赖平台的最主要原因。

礼物的收发在技术实现上也是用聊天室接口做的，通常采用IM中的自定义消息实现，当用户收到或发送礼物时将自定义消息对应的礼物图形渲染出来。另外，面对大量用户刷礼物时，礼物系统对一致性要求就比较高了，所以在实现上存一份数据建多条索引是一种很好的选择，也可以降低对事务的依赖。