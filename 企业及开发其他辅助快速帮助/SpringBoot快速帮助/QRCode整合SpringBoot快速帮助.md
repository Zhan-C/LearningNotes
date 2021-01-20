# 一、什么是QRCode？（什么是二维码）

QR码属于矩阵式二维码中的一个种类，由DENSO(日本电装)公司开发，由JIS和ISO将其标准化。QR码的样子其实在很多场合已经能够被看到了

![img](https://img-blog.csdn.net/20141223101235304?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bGluYTYwMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

1. 位置探测图形、位置探测图形分隔符：用于对二维码的定位，对每个QR码来说，位置都是固定存在的，只是大小规格会有所差异；这些黑白间隔的矩形块很容易进行图像处理的检测。 

2. 校正图形：根据尺寸的不同，矫正图形的个数也不同。矫正图形主要用于QR码形状的矫正，尤其是当QR码印刷在不平坦的面上，或者拍照时候发生畸变等。

3. 定位图形：这些小的黑白相间的格子就好像坐标轴，在二维码上定义了网格。

4. 格式信息：表示该二维码的纠错级别，分为L、M、Q、H； 

5. 数据区域：使用黑白的二进制网格编码内容。8个格子可以编码一个字节。

6. 版本信息：即二维码的规格，QR码符号共有40种规格的矩阵（一般为黑白色），从21x21（版本1），到177x177（版本40），每一版本符号比前一版本 每边增加4个模块。 

7. 纠错码字：用于修正二维码损坏带来的错误。



# 二、SpringBoot整合QRCode

## 1、Maven引入依赖（其余依赖不额外举例）

```xml
		<dependency>
            <groupId>com.google.zxing</groupId>
            <artifactId>core</artifactId>
            <version>3.3.0</version>
        </dependency>
```

## 2、编写工具类

```java
import com.google.zxing.BarcodeFormat;
import com.google.zxing.EncodeHintType;
import com.google.zxing.MultiFormatWriter;
import com.google.zxing.WriterException;
import com.google.zxing.common.BitMatrix;
import com.google.zxing.qrcode.decoder.ErrorCorrectionLevel;

import javax.imageio.ImageIO;
import java.awt.*;
import java.awt.image.BufferedImage;
import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.util.HashMap;

/**
 * @Author: AChuang
 * @Date: 2021/1/20 11:27
 * @Description
 */
public class QRCodeUtil {

    /**
     * 黑色
     */
    private static final int BLACK = 0xFF000000;

    /**
     * 白色
     */
    private static final int WHITE = 0xFFFFFFFF;

    /**
     * 默认长度
     */
    private static final int DEFAULT_WIDTH = 400;

    /**
     * 默认高度
     */
    private static final int DEFAULT_HEIGHT = 400;

    /**
     * 默认边距
     */
    private static final int DEFAULT_MARGIN = 1;

    /**
     * 默认编码格式
     */
    private static final String DEFAULT_CHARSET = "utf-8";

    /**
     * 默认图片类型
     */
    private static final String DEFAULT_IMG_TYPE = "png";

    /**
     * LOGO图片的默认比例按照几分之1
     */
    private static final int LOGO_PROPORTION = 4;


    /**
     * 根据url生成二维码并且返回字节数组
     *
     * @param url
     * @param width
     * @param height
     * @return
     */
    public static byte[] generateCode(String url, Integer width, Integer height, Integer margin) {
        HashMap hints = new HashMap<>();
        // 设置字符集
        hints.put(EncodeHintType.CHARACTER_SET, DEFAULT_CHARSET);
        // 设置容错等级；因为有了容错，在一定范围内可以把二维码p成你喜欢的样式,容错越高，二维码越密集
        hints.put(EncodeHintType.ERROR_CORRECTION, ErrorCorrectionLevel.H);
        // 设置外边距;(即白色区域)
        hints.put(EncodeHintType.MARGIN, margin);

        BitMatrix bitMatrix = null;
        try {
            bitMatrix = new MultiFormatWriter().encode(url, BarcodeFormat.QR_CODE, width, height, hints);
        } catch (WriterException e) {
            e.printStackTrace();
        }
        BufferedImage image = toBufferedImage(bitMatrix);
        ByteArrayOutputStream os = new ByteArrayOutputStream();
        try {
            ImageIO.write(image, DEFAULT_IMG_TYPE, os);
        } catch (IOException e) {
            e.printStackTrace();
        }
        return os.toByteArray();
    }

    /**
     * 重载方法
     *
     * @param url
     * @param width
     * @param height
     * @return
     */
    public static byte[] generateCode(String url, Integer width, Integer height) {
        return generateCode(url, width, height, DEFAULT_MARGIN);
    }

    /**
     * 重载方法
     *
     * @param url
     * @return
     */
    public static byte[] generateCode(String url) {
        return generateCode(url, DEFAULT_WIDTH, DEFAULT_HEIGHT);
    }

    /**
     * 生成二维码并且添加Logo
     *
     * @param url
     * @return
     */
    public static byte[] generateCodeSaveLogo(String url, Integer width, Integer height, Integer margin, InputStream logo) {
        // 生成二维码
        byte[] bytes = generateCode(url, width, height, margin);
        // 获取二维码输入流
        ByteArrayInputStream code = new ByteArrayInputStream(bytes);
        try {
            // 创建二维码图片流
            BufferedImage codeImg = ImageIO.read(code);
            // 创建Logo图片流
            BufferedImage logoImg = ImageIO.read(logo);
            // 创建画板工具
            Graphics2D image = codeImg.createGraphics();
            // 设置比例，以及xy轴
            int widthLogo = codeImg.getWidth() / LOGO_PROPORTION;
            int heightLogo = codeImg.getHeight() / LOGO_PROPORTION;
            int x = (codeImg.getWidth() - widthLogo) / 2;
            int y = (codeImg.getHeight() - heightLogo) / 2;

            // 设置颜色以及Logo放置位置
            image.drawImage(logoImg, x, y, widthLogo, heightLogo, null);
            image.drawRoundRect(x, y, widthLogo, heightLogo, 10, 10);
            image.setStroke(new BasicStroke(1));
            image.setColor(Color.WHITE);
            image.drawRect(x, y, widthLogo, heightLogo);
            image.dispose();
            ByteArrayOutputStream os = new ByteArrayOutputStream();
            ImageIO.write(codeImg, DEFAULT_IMG_TYPE, os);
            return os.toByteArray();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }

    /**
     * 重载方法
     *
     * @param url
     * @param width
     * @param height
     * @param logo
     * @return
     */
    public static byte[] generateCodeSaveLogo(String url, Integer width, Integer height, InputStream logo) {
        return generateCodeSaveLogo(url, width, height, DEFAULT_MARGIN, logo);
    }

    /**
     * 重载方法
     *
     * @param url
     * @param logo
     * @return
     */
    public static byte[] generateCodeSaveLogo(String url, InputStream logo) {
        return generateCodeSaveLogo(url, DEFAULT_WIDTH, DEFAULT_HEIGHT, logo);
    }


    /**
     * 根据BitMatrix生成图片流
     *
     * @param matrix
     * @return
     */
    private static BufferedImage toBufferedImage(BitMatrix matrix) {
        int width = matrix.getWidth();
        int height = matrix.getHeight();
        BufferedImage image = new BufferedImage(width, height, BufferedImage.TYPE_INT_RGB);
        for (int x = 0; x < width; x++) {
            for (int y = 0; y < height; y++) {
                image.setRGB(x, y, matrix.get(x, y) ? BLACK : WHITE);
            }
        }
        return image;
    }

}
```

## 3、使用Controller测试

```java
/**
 * @Author: AChuang
 * @Date: 2021/1/20 11:33
 * @Description
 */
@RestController
@RequestMapping("code")
@Api(tags = "各种码生成")
public class CreatQRCodeController {

    @ApiOperation("根据地址生成二维码")
    @GetMapping("qcCode")
    public void qcCode(String url, HttpServletResponse response) throws IOException {
        byte[] bytes = QRCodeUtil.generateCode(url);
        response.setContentType("image/png");
        response.getOutputStream().write(bytes);
    }
    
    @ApiOperation("根据地址加上logo图片生成二维码")
    @PostMapping("qcCodeByLogo")
    public void qcCodeByLogo(String url, MultipartFile logo, HttpServletResponse response) throws IOException {
        byte[] bytes = QRCodeUtil.generateCodeSaveLogo(url, logo.getInputStream());
        response.setContentType("image/png");
        response.getOutputStream().write(bytes);
    }

}
```

# 三、测试

### 测试一：

![image-20210120140731576](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210120140731576.png)

![image-20210120140808651](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210120140808651.png)

### 测试二：

![image-20210120140922648](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210120140922648.png)

![image-20210120140937251](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210120140937251.png)