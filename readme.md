# 萌芽后门总结
对萌芽引擎中存在的所有后门和可能的泄露隐私点进行总结，其中包括混淆器自动插入的和如何处理这些后门，有空再补充mod部分
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
mod部分反混淆复杂且后门危害性较强，留待后续补充，先写好插件部分