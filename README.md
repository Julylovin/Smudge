import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONObject;
import hsa.spc.sdk.request.SpcClientUtils;
import org.junit.Test;

import java.math.BigDecimal;

/**
 * 收银台服务请求调用示例
 *
 */
public class SpcClientTest {

    String smcReqUrl = "http://58.33.119.64/pmc/api/spc";
    /**
     * 版本号
     */
    String version = "2.0.1";
    /**
     * 渠道id
     */
    String appId = "8680E5AB99D74DBE96F8CAC5CEFAC232";
    /**
     * 报文加密秘钥
     */
    String appSecret = "F75E27032F5541A7943C2A7F2615F80A";
    /**
     * 渠道签名私钥，接入方自己生成及保管
     */
    String appPrivateKey = "PgpKxcTtBLzg3/VHc383ulbSqQa2rmGlEUidhrKTImw=";
    /**
     * 渠道签名公钥，接入方提供给平台端
     */
    String appPublicKey = "BDaGVfyFrWym7Aq2Ueit0j7dIYmSY1bsxCGN9TFxdmunS2PDMwpC4No4iSTynMnwZyD4u8sxHlajHwQVKjBgP2w=";
    /**
     * 平台端签名公钥，收银台平台端提供给接入方
     */
    String plafPublicKey = "BPAX8QWe3+2rw/1UnYynjWcNrRV+elFLsLrRX7sPtOFsXu9ItEr6rXo+vGxMd5LYIFTTbjFU3nPHaLt21Msgwtc=";

    /**
     * 请求收银台服务接口调用示例
     * 用于4.1、4.2、4.3、4.4接口调用
     * 按接口文档填写业务参数调用 SpcClientUtils.sendRequest
     */
    @Test
    public void testRequest() {

        try {
            //请求4.1支付下单接口地址调用示例
            String apiUrl = smcReqUrl + "/unifiedOrder/UNION_CHS";
            //支付下单接口业务参数，按实际业务填充参数
            JSONObject reqDto = new JSONObject();
            String payOrdId = "OORD350100202208231510102000000067";
            reqDto.put("orgCode", "H35010200227");
            reqDto.put("idNo", "XXXXXXXXX");
            reqDto.put("userName", "XXX");
            reqDto.put("idType", "01");
            reqDto.put("payOrdId", payOrdId);
            reqDto.put("operator", "H5");
            reqDto.put("outTradeNo", "O" + payOrdId);
            reqDto.put("orgName", "福建中医药大学附属第二人民医院");
            reqDto.put("frontBackUrl", "https://wwww.so.com");
            reqDto.put("syncCalUrl", "https://www.baidu.com");
            reqDto.put("amount", new BigDecimal("0.01"));
            reqDto.put("feeSumamt", new BigDecimal("0.03"));
            reqDto.put("fundPayAmt", new BigDecimal("0.02"));
            reqDto.put("othPayAmt", 0);
            JSONObject respDto = SpcClientUtils.sendRequest(apiUrl, appId, appSecret, appPrivateKey, plafPublicKey, version, reqDto, 3000, 3000);
            System.out.println(JSON.toJSONString(respDto));
            System.out.println(respDto.getString("cashierUrl"));
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 解密收银台前端回调参数
     * <p>
     * 前端回调示例：
     * https://www.so.com/?appId=8680E5AB99D74DBE96F8CAC5CEFAC232&encData=72e6568aef0343d4008bc75a51fc4d9881dda3cc65a7696109e190b3a3ea4972776048fd2e7f60402a37c00f1b22651ada40c17c50292efefc01c00c1206cc9c89a92a7c8ca4a41325b0a1cd0348d4d54e053ec0a8bfc45cfd2cb2214d3e6c10d9fcc31f952a9c255f0d519d78bb15c2ab5c8be4b4b27c777411b24f3b5ce56f4846ea7814f088968a9117d20af7be5e42b87280c7577c3bf775b41a1406090507cf7f03f326a8b5d373c66cb280e17c6bb2631217f5fe53a53a11d14936d81735059885af17031c76400b26443307e49a07a06049f8db408070c6709080c98998dd37c0a49147c7b028d879cbd07fe5ce2fdb6b2b052ade65a8db72d144f4e3a3570e3d9721c5103ec4e214370ea257e9d8d784cf40258c50b4cced1e42cec726b19327c93861d4b8ac13136e245f8950886ebe05b4eca06fd780cc5a4fe5fc
     * 取回调地址上的encData进行解密
     */
    @Test
    public void redirectDecrypt() {
        try {
            String encData = "72e6568aef0343d4008bc75a51fc4d9881dda3cc65a7696109e190b3a3ea4972776048fd2e7f60402a37c00f1b22651ada40c17c50292efefc01c00c1206cc9c89a92a7c8ca4a41325b0a1cd0348d4d54e053ec0a8bfc45cfd2cb2214d3e6c10d9fcc31f952a9c255f0d519d78bb15c2ab5c8be4b4b27c777411b24f3b5ce56f4846ea7814f088968a9117d20af7be5e42b87280c7577c3bf775b41a1406090507cf7f03f326a8b5d373c66cb280e17c6bb2631217f5fe53a53a11d14936d81735059885af17031c76400b26443307e49a07a06049f8db408070c6709080c98998dd37c0a49147c7b028d879cbd07fe5ce2fdb6b2b052ade65a8db72d144f4e3a3570e3d9721c5103ec4e214370ea257e9d8d784cf40258c50b4cced1e42cec726b19327c93861d4b8ac13136e245f8950886ebe05b4eca06fd780cc5a4fe5fc";
            JSONObject decData = SpcClientUtils.redirectDecrypt(appId, appSecret, encData);
            System.out.println("解密结果：" + JSONObject.toJSONString(decData));
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 解密、验签收银台后端回调请求
     * <p>
     * 后端回调的报文示例：
     * {"encType":"SM4","appId":"8680E5AB99D74DBE96F8CAC5CEFAC232","signData":"YrIrYKeCYzPZzdCDrzP2EEJiAOE12Z61CXjDPyRXluNiIHeNWUKHlAOUgVA/g99g+pW+o+BXJkBOtla9tcju3A==","signType":"SM2","encData":"3aa85bd8d36e4e389d7c67d0d676ffc41eef8bc7b92195826b7fb8dcd89eb8495319df47daacf8352452550670db353eda40c17c50292efefc01c00c1206cc9c89a92a7c8ca4a41325b0a1cd0348d4d54e053ec0a8bfc45cfd2cb2214d3e6c10d9fcc31f952a9c255f0d519d78bb15c2ab5c8be4b4b27c777411b24f3b5ce56f4846ea7814f088968a9117d20af7be5e42b87280c7577c3bf775b41a1406090507cf7f03f326a8b5d373c66cb280e17c6f1fd62921568c668b8c972d79347bd835059885af17031c76400b26443307e49a07a06049f8db408070c6709080c98998dd37c0a49147c7b028d879cbd07fe5ce2fdb6b2b052ade65a8db72d144f4e3de14c017ba5eda03b286611ce1acccc3e9d8d784cf40258c50b4cced1e42cec726b19327c93861d4b8ac13136e245f89c7bbb627b2dd32f7f411bb2db0cbe6f3","version":"2.0.1","timestamp":1661239946874}
     */

    @Test
    public void decAndVerifyRequest() {
        try {
            //收银台后端回调请求院端报文
            String plafRequest = "{\"encType\":\"SM4\",\"appId\":\"8680E5AB99D74DBE96F8CAC5CEFAC232\",\"signData\":\"YrIrYKeCYzPZzdCDrzP2EEJiAOE12Z61CXjDPyRXluNiIHeNWUKHlAOUgVA/g99g+pW+o+BXJkBOtla9tcju3A==\",\"signType\":\"SM2\",\"encData\":\"3aa85bd8d36e4e389d7c67d0d676ffc41eef8bc7b92195826b7fb8dcd89eb8495319df47daacf8352452550670db353eda40c17c50292efefc01c00c1206cc9c89a92a7c8ca4a41325b0a1cd0348d4d54e053ec0a8bfc45cfd2cb2214d3e6c10d9fcc31f952a9c255f0d519d78bb15c2ab5c8be4b4b27c777411b24f3b5ce56f4846ea7814f088968a9117d20af7be5e42b87280c7577c3bf775b41a1406090507cf7f03f326a8b5d373c66cb280e17c6f1fd62921568c668b8c972d79347bd835059885af17031c76400b26443307e49a07a06049f8db408070c6709080c98998dd37c0a49147c7b028d879cbd07fe5ce2fdb6b2b052ade65a8db72d144f4e3de14c017ba5eda03b286611ce1acccc3e9d8d784cf40258c50b4cced1e42cec726b19327c93861d4b8ac13136e245f89c7bbb627b2dd32f7f411bb2db0cbe6f3\",\"version\":\"2.0.1\",\"timestamp\":1661239946874}";
            JSONObject plafRequestBody = JSONObject.parseObject(plafRequest);
            plafRequestBody = SpcClientUtils.decAndVerifyPlafRequest(appId, appSecret, plafPublicKey, plafRequestBody);
            System.out.println("解密、验签收银台回调请求报文结果：" + JSONObject.toJSONString(plafRequestBody));
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}



