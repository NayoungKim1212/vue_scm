<template>
  <dl id="cusInfoWrap">
    <dd class="content"></dd>
    <!-- s : 여기에 내용입력 -->
    <table class="row" table id="cusInfo">
      <caption>
        caption
      </caption>

      <tbody>
        <tr>
          <td colspan="4" class="text-center">
            <div class="my-4">
              <strong style="font-size: 30px">{{ title }}</strong>
            </div>
          </td>
        </tr>

        <tr>
          <th scope="row">로그인ID</th>
          <td>
            <input type="text" class="form-control" name="loginID" id="loginID" v-model="loginID" />
          </td>
          <th scope="row">기업코드</th>
          <td>
            <input type="text" class="form-control" name="company_cd" id="company_cd" v-model="company_cd" />
          </td>
        </tr>
        <tr>
          <th scope="row">기업명</th>
          <td>
            <input type="text" class="form-control" name="company_nm" id="company_nm" v-model="company_nm" />
          </td>
          <th scope="row">담당자명</th>
          <td>
            <input type="text" class="form-control" name="company_mng_nm" id="company_mng_nm"
              v-model="company_mng_nm" />
          </td>
        </tr>
        <tr>
          <th scope="row">연락처</th>
          <input type="text" class="form-control" name="mng_tel" id="mng_tel" v-model="mng_tel" />
          <th scope="row">메일</th>
          <input type="text" class="form-control" name="mail" id="mail" v-model="mail" />
        </tr>

        <tr>
          <th scope="row">은행</th>
          <input type="text" class="form-control" name="bank" id="bank" v-model="bank" />
          <th scope="row">계좌번호</th>
          <input type="text" class="form-control" name="account" id="account" v-model="account" />
        </tr>

        <tr v-if="paction == 'I'">
          <th scope="row">우편번호</th>
          <td>
            <input type="text" class="form-control" name="zip_code" id="zip_code" readOnly v-model="zip_code" />
          </td>
          <button @click="zipcodeSearch">우편번호 찾기</button>
        </tr>
        <tr v-if="paction == 'U'">
          <th scope="row">우편번호</th>
          <td>
            <input type="text" class="form-control" readonly v-model="zip_code" />
          </td>
          <th scope="row">가입일자</th>
          <td>
            <input type="text" class="form-control" name="join_date" id="join_date" v-model="join_date" />
          </td>
        </tr>

        <tr>
          <th scope="row">주소</th>
          <input type="text" class="form-control" name="addr" id="addr" readOnly v-model="addr" />
          <th scope="row">상세주소</th>
          <input type="text" class="form-control" name="addr_detail" id="addr_detail" v-model="addr_detail" />
        </tr>
        <tr>
          <th scope="row">활성화여부 변경</th>
          <td colspan="3">
            <select id="active" class="form-control" style="width: 150px" v-model="active">
              <option :value="active" disabled>Y</option>
              <option :value="active === 'Y' ? 'N' : 'Y'">N</option>
            </select>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- e : 여기에 내용입력 -->

    <div class="btn_areaC mt30">
      <a @click="save()" class="btn btn-primary" id="btnSaveGrpCod" name="btn">
        <span>저장</span>
      </a>
      <a @click="deleteCusinfo()"
        :class="{ 'btn btn-primary mx-2': delshow == false, 'btn btn-primary': delshow == true }">
        <span>삭제</span>
      </a>
      <a @click="cancel()" :class="{ 'btn btn-primary mx-2': delshow == false, 'btn btn-primary': delshow == true }">
        <span>닫기</span>
      </a>
    </div>
  </dl>
</template>

<script>
import { closeModal } from "jenesius-vue-modal";
function loadScript(src, callback) {
  const script = document.createElement('script');
  script.src = src;
  script.onload = () => callback();
  document.head.appendChild(script);
}

export default {
  // vue에서는 받아온 변수를 methods에서 직접 핸들링이 불가능하기 때문에
  // 임시 변수를 만들어서 받아온 변수를 넣어 줘야 함
  props: { title: String, loginId: String, action: String },
  data: function () {
    return {
      paction: this.action,
      loginID: this.loginId,
      company_nm: "",
      company_mng_nm: "",
      mng_tel: "",
      active: "Y",
      delshow: true,
      company_cd: "",
      mail: "",
      bank: "",
      account: "",
      zip_code: "",
      join_date: "",
      addr: "",
      addr_detail: "",
    };
  },
  computed: {},

  mounted: function () {
    let vm = this;
    // 신규 등록 시
    if (this.loginID == null || this.loginID == "") {
      vm.loginID = "";
      vm.company_nm = "";
      vm.company_mng_nm = "";
      vm.mng_tel = "";
      vm.active = "Y";
      vm.company_cd = "";
      vm.mail = "";
      vm.bank = "";
      vm.account = ""; ``
      vm.zip_code = "";
      vm.join_date = "";
      vm.addr = "";
      vm.addr_detail = "";
      vm.delshow = false;
    } else {
      // 수정시 해당하는 상세정보 가져옴
      let params = new URLSearchParams();
      params.append("loginID", this.loginID);
      console.log("params::::: " + params);

      this.axios
        .post("/scm/cusDetailInfo.do", params)
        .then(function (response) {
          console.log("응답 데이터:", response);

          if (response.data && Array.isArray(response.data.cusDetailInfo) && response.data.cusDetailInfo.length > 0) {
            const cusDetailInfo = response.data.cusDetailInfo[0]; // Access the first item of the array
            console.log("cusDetailInfo:", cusDetailInfo);
            vm.loginID = cusDetailInfo.loginID; // Make sure these property names match exactly with your server response
            vm.company_nm = cusDetailInfo.company_nm;
            vm.company_mng_nm = cusDetailInfo.company_mng_nm;
            vm.mng_tel = cusDetailInfo.mng_tel;
            vm.active = cusDetailInfo.active;
            vm.company_cd = cusDetailInfo.company_cd;
            vm.mail = cusDetailInfo.mail;
            vm.bank = cusDetailInfo.bank;
            vm.account = cusDetailInfo.account;
            vm.zip_code = cusDetailInfo.zip_code;
            vm.join_date = cusDetailInfo.join_date;
            vm.addr = cusDetailInfo.addr;
            vm.addr_detail = cusDetailInfo.addr_detail;
            vm.delshow = true;
          } else {
            console.error('cusDetailInfo is undefined.');
          }
        })
        .catch(function (error) {
          alert("에러! API 요청에 오류가 있습니다. " + error);
        });
    }
  },
  methods: {
    save: function () {

      if (confirm("저장하시겠습니까?")) {
        let vm = this;

        let params = new URLSearchParams();
        console.log("여기 타고");
        params.append("loginID", this.loginID);
        params.append("company_cd", this.company_cd);
        params.append("name", this.company_nm);
        params.append("company_mng", this.company_mng_nm);
        params.append("tel", this.mng_tel);
        params.append("mng_tel", this.mng_tel);
        params.append("mail", this.mail);
        params.append("bank", this.bank);
        params.append("detail_name", this.bank);
        params.append("account", this.account);
        params.append("zip_code", this.zip_code);
        params.append("addr", this.addr);
        params.append("addr_detail", this.addr_detail);
        params.append("active", this.active);
        console.log("params:::::::::::::::::::::: " + decodeURIComponent(params.toString()));

        if (this.paction == "I") {
          this.saveUrl = "/scm/sendCusActiveSave.do"
        } else if (this.paction == "U") {
          console.log("수정 시작");
          this.saveUrl = "/scm/sendCusActiveModify.do"
        }
        console.log("URL:", this.saveUrl);
        console.log("action:", this.action);
        this.axios({
          method: "post",
          url: this.saveUrl,
          data: params,
        })
          .then(function (response) {
            console.log("Response Data:", response.data);
            console.log("loginID: 값?", vm.loginID);
            console.log(response);
            let status = response.status;
            //let msg = response.data.resultMsg;
            if (status == 200) {
              alert("저장이 완료되었습니다.");
              closeModal(vm);
            } else {
              alert("API 요청에 오류가 있습니다.");
            }
          })
          .catch(function (error) {
            alert("에러! API 요청에 오류가 있습니다. " + error);
          });
      }
    },
    cancel: function () {
      let vm = this;
      closeModal(vm);
    },
    zipcodeSearch: function () {
      let vm = this;

      loadScript('https://t1.daumcdn.net/mapjsapi/bundle/postcode/prod/postcode.v2.js', function () {
        new daum.Postcode({
          oncomplete: function (data) {
            // 우편번호 검색 완료 후 로직
            var addr = '';
            var extraAddr = '';

            if (data.userSelectedType === 'R') {
              addr = data.roadAddress;
            } else {
              addr = data.jibunAddress;
            }

            if (data.userSelectedType === 'R') {
              if (data.bname !== '' && /[동|로|가]$/g.test(data.bname)) {
                extraAddr += data.bname;
              }
              if (data.buildingName !== '' && data.apartment === 'Y') {
                extraAddr += (extraAddr !== '' ? ', ' + data.buildingName : data.buildingName);
              }
            }

            // 우편번호와 주소 정보를 컴포넌트의 데이터에 할당
            vm.zip_code = data.zonecode;
            vm.addr = addr;
            vm.addr_detail = ''; // 상세 주소 초기화

            // 상세주소 입력란에 포커스
            // 이 부분은 실제 DOM 요소에 직접 접근하여 포커스를 주는 예시이며,
            // Vue에서는 데이터 바인딩을 통해 처리하는 것을 권장합니다.
            document.getElementById("addr_detail").focus();
          }
        }).open();
      });
    },
    deleteCusinfo: function () {
      if (confirm("삭제하시겠습니까?")) {
        let vm = this;
        let params = new URLSearchParams();
        params.append("loginID", this.loginID)

        this.axios
          .post("/scm/deleteCusinfoVue.do", params)
          .then(function (response) {
            alert(response.data.resultMsg);
            closeModal(vm);
          })
          .catch(function (error) {
            alert("에러입니다. API요청에 오류가 있습니다. " + error);
          });
      }
    },
  }
}



</script>

<style>
#cusInfoWrap {
  background-color: #fff;
  padding: 3rem;
  border-radius: 10px;
  border: 2px solid rgba(59, 59, 59, 0);
  overflow-y: auto;
  /* 필요한 경우 스크롤바를 추가 */
  max-height: 90vh
}
</style>