### 🏆 2022 캡스톤 디자인 경진대회 최우수상 수상      <br>

## P2D (Play to Donate)<br>
**블록체인 기반 실시간 기부 플랫폼**
## 시연 영상 https://youtu.be/V75_MES1wCI<br>
## P2D Scan Site [P2D Scan](https://62aaa9bef3e87a28946e90d6--stellar-creponne-69e72f.netlify.app/)




## 프로젝트 흐름도 <br>
![image](https://user-images.githubusercontent.com/79950504/174436552-c50d0d72-d0a7-4ea9-9d23-18a6a634c95d.png)



<br><br>

## 1. 💡 소개 <br>
- 금전적인 부담감, 불투명한 기부과정, 기부단체에 대한 신뢰성부족 등 다양한 이유로 많은 사람들이 기부를 꺼리고 있다.

- 기부의 신뢰성을 제공하고 투명성을 확보하며 기부에 대한 불신을 해소해 올바른 기부문화 확산에 기여하고자 개발을 진행하게 된다.

- 메타마스크를 통해 간단한 로그인을 진행하고, 간단한 게임을 플레이한 뒤 그 점수에 비례하여 P2D 토큰을 발급 받을 수 있다.

- 지정된 기부처(특정 지갑주소)로만 토큰을 전송할 수 있도록 Smart Contract에 White List 조건을 등록하였다.

- 기부가 이루어지는 동안 자체 개발한 트랜잭션 스캔 사이트를 띄워줘 기부의 흐름을 직접적으로 확인할 수 있다.

- 이후 1회에 한하여 기부증서 NFT를 발급 받을 수 있다.


<br>


## 2. 💻 Language&Tools&API <br>
* Remix IDE (Solidity)  
* Unity (C#)  
* Eth Scan API (Web3.js, html, JavaScript)  

    
<br><br>

## 3. 🛠 기능구현 <br>

 ![site](https://user-images.githubusercontent.com/79950091/174741740-d33d138c-4c4f-4389-8e8e-9753b62a0197.png)
### 3.1 P2D transaction monitoring

Web3.js와 JavaScript를 사용한 P2D 플랫폼 내의 donate transaction 현황을 조회 가능한 사이트를 구현하였다. Txn Hash를 클릭하여 클릭한 transaction의
Details 조회가 가능하다.

![intro](https://user-images.githubusercontent.com/79950091/174742132-39e9f7ef-7852-43b7-a0fc-236cad3ddc41.png)
### 3.2 P2D main interface

Main interface의 Start button을 통해 Metamask로그인을 통하여 게임에 접속가능하다.

![main](https://user-images.githubusercontent.com/79950091/174742235-925d3a54-9cb5-4fe7-bebe-3d7c3da27dd4.png)
### 3.3 P2D In-game interface

Character 상단에 metamask 계정 주소와 좌측 상단의 보유한 토큰의 개수, 갤러리 형식의In-game을 통해 Play를 진행한다.

![tokendeploy](https://user-images.githubusercontent.com/79950091/174958389-61d212a9-f9c9-4a07-8ae4-71f59f2e68b1.png)
### 3.4 P2D Token deploy

SmartContract로 작성한 ERC-20의 토큰 minting과 
발급 후의 transaction조회를 수행하는 동작이다.

![tokendonate](https://user-images.githubusercontent.com/79950091/174958629-dd54eaff-75d7-470f-844b-299cf112b423.png)
### 3.5 P2D Token Donate

SmartContract로 작성한 ERC-20 기반 토큰의 Donate와 
Donate 후의transaction 조회를 수행하는 동작이다. 해당 Donation의 주소 정보를 Whitelist에 등록시켜    
사용자가 다른 지갑 주소로 전송하지 못하도록 구현하였다.

![NFT](https://user-images.githubusercontent.com/79950091/174958633-524ce647-fc71-4110-8947-f3dc7ba1092e.png)
### 3.6 P2D NFT Deploy

SmartContract로 작성한 ERC-721(NFT)의 발급과 발급된 NFT정보를
Opensea(NFT거래소)에서 확인하는 동작이다.
NFT 발급자는 isNFT = true로 등록시켜 기부자 당 1회만 NFT발급이 가능하도록 구현하였다.
<br><br>
'''

using UnityEngine;
using UnityEngine.UI;
using System.Threading.Tasks;
using TMPro;

public class TestSolidityScore : MonoBehaviour
{
    //public InputField input;
    public TMP_Text firstRank;
    public TMP_Text secondtRank;
    public TMP_Text thirdRank;
    public TMP_Text fourthRank;
    public TMP_Text fifthRank;

    public TMP_Text address;

    //public Text data;
   
    public Text balanceTxt;

    public Button Donatebtn;
    //public Button Rankbtn;
    public Button GetTokenbtn;

    private float TimeLeft = 1.0f;
    private float NextTime = 0.0f;

    // 프로젝트 시작시
    public void Start()
    {
        var myaddress = PlayerPrefs.GetString("Account");
        address.text = myaddress;
    }

    // 1초마다 Rank 새로고침
    async void Update()
    {
        if (Time.time > NextTime)
        {
            NextTime = Time.time + TimeLeft;
            await Refresh();
        }

    }

    // 스마트계약에서 실행된 score Rank 나열(get)
    async Task Refresh()
    {
        decimal dee = 1000000000000000000;
        var address = PlayerPrefs.GetString("Account");

        string chain = "ethereum";
        // set network mainnet, testnet
        string network = "rinkeby";

        var ABI = "[{\"inputs\":[],\"stateMutability\":\"nonpayable\",\"type\":\"constructor\"},{\"anonymous\":false,\"inputs\":[{\"indexed\":true,\"internalType\":\"address\",\"name\":\"account\",\"type\":\"address\"}],\"name\":\"AddedToWhitelist\",\"type\":\"event\"},{\"anonymous\":false,\"inputs\":[{\"indexed\":true,\"internalType\":\"address\",\"name\":\"owner\",\"type\":\"address\"},{\"indexed\":true,\"internalType\":\"address\",\"name\":\"spender\",\"type\":\"address\"},{\"indexed\":false,\"internalType\":\"uint256\",\"name\":\"value\",\"type\":\"uint256\"}],\"name\":\"Approval\",\"type\":\"event\"},{\"anonymous\":false,\"inputs\":[{\"indexed\":true,\"internalType\":\"address\",\"name\":\"account\",\"type\":\"address\"}],\"name\":\"RemovedFormWhitelist\",\"type\":\"event\"},{\"anonymous\":false,\"inputs\":[{\"indexed\":true,\"internalType\":\"address\",\"name\":\"from\",\"type\":\"address\"},{\"indexed\":true,\"internalType\":\"address\",\"name\":\"to\",\"type\":\"address\"},{\"indexed\":false,\"internalType\":\"uint256\",\"name\":\"value\",\"type\":\"uint256\"}],\"name\":\"Transfer\",\"type\":\"event\"},{\"inputs\":[],\"name\":\"_addr\",\"outputs\":[{\"internalType\":\"address payable\",\"name\":\"\",\"type\":\"address\"}],\"stateMutability\":\"view\",\"type\":\"function\"},{\"inputs\":[{\"internalType\":\"address\",\"name\":\"\",\"type\":\"address\"}],\"name\":\"_userscore\",\"outputs\":[{\"internalType\":\"uint256\",\"name\":\"\",\"type\":\"uint256\"}],\"stateMutability\":\"view\",\"type\":\"function\"},{\"inputs\":[{\"internalType\":\"address\",\"name\":\"_addr\",\"type\":\"address\"}],\"name\":\"add_Whitelist\",\"outputs\":[],\"stateMutability\":\"nonpayable\",\"type\":\"function\"},{\"inputs\":[{\"internalType\":\"address\",\"name\":\"owner\",\"type\":\"address\"},{\"internalType\":\"address\",\"name\":\"spender\",\"type\":\"address\"}],\"name\":\"allowance\",\"outputs\":[{\"internalType\":\"uint256\",\"name\":\"\",\"type\":\"uint256\"}],\"stateMutability\":\"view\",\"type\":\"function\"},{\"inputs\":[],\"name\":\"amount\",\"outputs\":[{\"internalType\":\"uint256\",\"name\":\"\",\"type\":\"uint256\"}],\"stateMutability\":\"view\",\"type\":\"function\"},{\"inputs\":[{\"internalType\":\"address\",\"name\":\"spender\",\"type\":\"address\"},{\"internalType\":\"uint256\",\"name\":\"amount\",\"type\":\"uint256\"}],\"name\":\"approve\",\"outputs\":[{\"internalType\":\"bool\",\"name\":\"\",\"type\":\"bool\"}],\"stateMutability\":\"nonpayable\",\"type\":\"function\"},{\"inputs\":[{\"internalType\":\"address\",\"name\":\"account\",\"type\":\"address\"}],\"name\":\"balanceOf\",\"outputs\":[{\"internalType\":\"uint256\",\"name\":\"\",\"type\":\"uint256\"}],\"stateMutability\":\"view\",\"type\":\"function\"},{\"inputs\":[],\"name\":\"decimals\",\"outputs\":[{\"internalType\":\"uint8\",\"name\":\"\",\"type\":\"uint8\"}],\"stateMutability\":\"view\",\"type\":\"function\"},{\"inputs\":[{\"internalType\":\"address\",\"name\":\"spender\",\"type\":\"address\"},{\"internalType\":\"uint256\",\"name\":\"subtractedValue\",\"type\":\"uint256\"}],\"name\":\"decreaseAllowance\",\"outputs\":[{\"internalType\":\"bool\",\"name\":\"\",\"type\":\"bool\"}],\"stateMutability\":\"nonpayable\",\"type\":\"function\"},{\"inputs\":[{\"internalType\":\"uint256\",\"name\":\"_number\",\"type\":\"uint256\"}],\"name\":\"getUserScore\",\"outputs\":[{\"internalType\":\"address\",\"name\":\"user_addr\",\"type\":\"address\"},{\"internalType\":\"uint256\",\"name\":\"user_score\",\"type\":\"uint256\"}],\"stateMutability\":\"view\",\"type\":\"function\"},{\"inputs\":[{\"internalType\":\"address\",\"name\":\"spender\",\"type\":\"address\"},{\"internalType\":\"uint256\",\"name\":\"addedValue\",\"type\":\"uint256\"}],\"name\":\"increaseAllowance\",\"outputs\":[{\"internalType\":\"bool\",\"name\":\"\",\"type\":\"bool\"}],\"stateMutability\":\"nonpayable\",\"type\":\"function\"},{\"inputs\":[{\"internalType\":\"address\",\"name\":\"_address\",\"type\":\"address\"}],\"name\":\"isWhitelisted\",\"outputs\":[{\"internalType\":\"bool\",\"name\":\"\",\"type\":\"bool\"}],\"stateMutability\":\"view\",\"type\":\"function\"},{\"inputs\":[],\"name\":\"name\",\"outputs\":[{\"internalType\":\"string\",\"name\":\"\",\"type\":\"string\"}],\"stateMutability\":\"view\",\"type\":\"function\"},{\"inputs\":[{\"internalType\":\"address\",\"name\":\"_address\",\"type\":\"address\"}],\"name\":\"remove_whitelist\",\"outputs\":[],\"stateMutability\":\"nonpayable\",\"type\":\"function\"},{\"inputs\":[{\"internalType\":\"uint256\",\"name\":\"_score\",\"type\":\"uint256\"}],\"name\":\"reward\",\"outputs\":[],\"stateMutability\":\"nonpayable\",\"type\":\"function\"},{\"inputs\":[],\"name\":\"score\",\"outputs\":[{\"internalType\":\"uint256\",\"name\":\"\",\"type\":\"uint256\"}],\"stateMutability\":\"view\",\"type\":\"function\"},{\"inputs\":[{\"internalType\":\"uint256\",\"name\":\"_value\",\"type\":\"uint256\"}],\"name\":\"send\",\"outputs\":[],\"stateMutability\":\"payable\",\"type\":\"function\"},{\"inputs\":[],\"name\":\"symbol\",\"outputs\":[{\"internalType\":\"string\",\"name\":\"\",\"type\":\"string\"}],\"stateMutability\":\"view\",\"type\":\"function\"},{\"inputs\":[{\"internalType\":\"uint256\",\"name\":\"\",\"type\":\"uint256\"}],\"name\":\"topScores\",\"outputs\":[{\"internalType\":\"address\",\"name\":\"addr\",\"type\":\"address\"},{\"internalType\":\"uint256\",\"name\":\"score\",\"type\":\"uint256\"}],\"stateMutability\":\"view\",\"type\":\"function\"},{\"inputs\":[],\"name\":\"totalSupply\",\"outputs\":[{\"internalType\":\"uint256\",\"name\":\"\",\"type\":\"uint256\"}],\"stateMutability\":\"view\",\"type\":\"function\"},{\"inputs\":[{\"internalType\":\"address\",\"name\":\"to\",\"type\":\"address\"},{\"internalType\":\"uint256\",\"name\":\"amount\",\"type\":\"uint256\"}],\"name\":\"transfer\",\"outputs\":[{\"internalType\":\"bool\",\"name\":\"\",\"type\":\"bool\"}],\"stateMutability\":\"nonpayable\",\"type\":\"function\"},{\"inputs\":[{\"internalType\":\"address\",\"name\":\"from\",\"type\":\"address\"},{\"internalType\":\"address\",\"name\":\"to\",\"type\":\"address\"},{\"internalType\":\"uint256\",\"name\":\"amount\",\"type\":\"uint256\"}],\"name\":\"transferFrom\",\"outputs\":[{\"internalType\":\"bool\",\"name\":\"\",\"type\":\"bool\"}],\"stateMutability\":\"nonpayable\",\"type\":\"function\"}]";
        var contract = "0x8cEdbB2dDf799d486ef4544663AAC7092547f55e";

        // smart contract method to call
        string method = "getUserScore";
        string method2 = "balanceOf";
        string firstArg = "[\"0\"]";
        string secondArg = "[\"1\"]";
        string thirdArg = "[\"2\"]";
        string fourthArg = "[\"3\"]";
        string fifthArg = "[\"4\"]";
        string account = "[\"" + address + "\"]";
        // connects to user's browser wallet to call a transaction
        string first = await EVM.Call(chain, network, contract, ABI, method, firstArg);
        string second = await EVM.Call(chain, network, contract, ABI, method, secondArg);
        string third = await EVM.Call(chain, network, contract, ABI, method, thirdArg);
        string fourth = await EVM.Call(chain, network, contract, ABI, method, fourthArg);
        string fifth = await EVM.Call(chain, network, contract, ABI, method, fifthArg);
        string balance = await EVM.Call(chain, network, contract, ABI, method2, account);

        // " 기준으로 문자열 자르는 과정
        char sp = '"';

        // 나누어진 문자열을 배열에 저장
        string[] firstStr = first.Split(sp);
        string[] secondStr = second.Split(sp);
        string[] thirdStr = third.Split(sp);
        string[] fourthStr = fourth.Split(sp);
        string[] fifthStr = fifth.Split(sp);

        // 필요한 문자열만 추출
        try
        {
            firstRank.text = firstStr[3] + "    " + firstStr[7];
            secondtRank.text = secondStr[3] + "    " + secondStr[7];
            thirdRank.text = thirdStr[3] + "    " + thirdStr[7];
            fourthRank.text = fourthStr[3] + "    " + fourthStr[7];
            fifthRank.text = fifthStr[3] + "    " + fifthStr[7];
        }
        catch
        {
            firstRank.text = "UnRanked";
            secondtRank.text = "UnRanked";
            thirdRank.text = "UnRanked";
            fourthRank.text = "UnRanked";
            fifthRank.text = "UnRanked";
        }

        try
        {
            balanceTxt.text = (decimal.Parse(balance) / dee).ToString();
        }
        catch
        {
            balanceTxt.text = "0";
        }
    }
}
'''


<br><br><br><br>

## 4.🧩 결론 <br>
Unity Web3GL을 통해 빌드 되었기 때문에 MetaMask로 간단한 로그인이 가능하다. 
이후 간단한 일회성 게임을 플레이하면 그 스코어에 비례해 자체 개발한 P2D 토큰을 발급 받을 수 있다. 
해당 토큰은 스마트 컨트랙트에 등록된 화이트 리스트 조건에 따라 지정된 주소로만 전송이 가능하다. 
본 과정을 통해 기부를 진행한 사용자는 1회에 한하여 NFT 기부증서를 발급 받을 수 있다. 
모든 기부 과정을 직접적으로 확인 가능한 트랜잭션 모니터링 사이트 역시 개발하였다. 
기부에 있어 큰 문제로 손꼽히고 있는 “경제적 여유”, “기부 단체에 대한 신뢰성”, “기부 과정의 투명성”을 해결할 수 있을것으로 기대된다.
<br><br>

![image](https://user-images.githubusercontent.com/74412438/196713643-24523058-dbae-4993-9dbd-4d9fae8bdeea.png)


