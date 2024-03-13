<template>
    <dl id="getModalContent">
      <dd class="content"></dd>
      <!-- Modal content -->
      <div
        style="
          height: 500px;
          display: flex;
          justify-content: center;
          background-color: #dce1e5;
          margin-bottom: 20px;
          vertical-align: text-bottom;
        "
      >
        <table class="row" style="text-align: center">
          <colgroup>
            <col width="px" />
            <col width="*" />
            <col width="px" />
            <col width="*" />
          </colgroup>
          <tbody>
            <tr>
              <td colspan="4" class="text-center">
                <div class="my-4">
                  <strong style="font-size: 30px">{{ title }}</strong>
                </div>
              </td>
            </tr>
  
            <tr>
              <th scope="row">로그인ID <span class="font_red">*</span></th>
              <td>
                <input
                  type="text"
                  class="form-control"
                  name="loginID"
                  id="loginID"
                  v-model="loginID"
                />
              </td>
              <th scope="row">기업코드</th>
              <td>
                <input
                  type="text"
                  class="form-control"
                  name="company_cd"
                  id="company_cd"
                  v-model="company_cd"
                />
              </td>
            </tr>
            <tr>
              <th scope="row">기업명</th>
              <td>
                <input
                  type="text"
                  class="form-control"
                  name="company_nm"
                  id="company_nm"
                  v-model="company_nm"
                />
              </td>
              <th scope="row">담당자명</th>
              <td>
                <input
                  type="text"
                  class="form-control"
                  name="company_mng_nm"
                  id="company_mng_nm"
                  v-model="company_mng_nm"
                />
              </td>
            </tr>
            <tr>
              <th scope="row">연락처</th>
              <td>
                <input
                  type="text"
                  class="form-control"
                  name="mng_tel"
                  id="mng_tel"
                  v-model="mng_tel"
                />
              </td>
              <th scope="row">메일</th>
              <td>
                <input
                  type="text"
                  class="form-control"
                  name="mail"
                  id="mail"
                  v-model="mail"
                />
              </td>
            </tr>
            <tr>
              <th scope="row">은행</th>
              <td>
                <input
                  type="text"
                  class="form-control"
                  name="bank"
                  id="bank"
                  v-model="bank"
                />
              </td>
              <th scope="row">계좌번호</th>
              <td>
                <input
                  type="text"
                  class="form-control"
                  name="account"
                  id="account"
                  v-model="account"
                />
              </td>
            </tr>
            <tr>
              <th scope="row">우편번호</th>
              <td>
                <input
                  type="text"
                  class="form-control"
                  name="zip_code"
                  id="zip_code"
                  v-model="zip_code"
                />
              </td>
              <th scope="row">가입일자</th>
              <td>
                <input
                  type="text"
                  class="form-control"
                  name="join_date"
                  id="join_date"
                  v-model="join_date"
                />
              </td>
            </tr>
            <tr>
              <th scope="row">주소</th>
              <td>
                <input
                  type="text"
                  class="form-control"
                  name="addr"
                  id="addr"
                  v-model="addr"
                />
              </td>
  
              <th scope="row">상세 주소</th>
              <td colspan="3">
                <input
                  type="text"
                  class="form-control"
                  name="addr_detail"
                  id="addr_detail"
                  v-model="addr_detail"
                />
              </td>
            </tr>
  
            <tr>
              <th scope="row">활성화여부 변경</th>
              <td>
                <select
                  name="active"
                  style="width: 40px; height: 30px"
                  v-model="active"
                >
                  <option :value="active" disabled>Y</option>
                  <option :value="active === 'Y' ? 'N' : 'Y'">N</option>
                </select>
              </td>
            </tr>
            <div class="btn_areaC mt30">
              <a
                @click="save()"
                class="btn btn-primary"
                id="btnSaveGrpCod"
                name="btn"
              >
                <span>저장</span>
              </a>
              <a
                @click="deleteCusinfo()"
                class="btn btn-danger mx-2"
                v-show="delshow"
              >
                <span>삭제</span>
              </a>
              <a
                @click="cancel()"
                :class="{
                  'btn btn-primary mx-2': delshow == false,
                  'btn btn-primary': delshow == true,
                }"
              >
                <span>취소</span>
              </a>
            </div>
          </tbody>
        </table>
      </div>
    </dl>
  </template>
  
  <script>
  import { closeModal } from "jenesius-vue-modal";
  export default {
    props: { title: String, pLoginID: String, pAction: String },
    data: function () {
      return {
        loginID: this.pLoginID,
        action: this.pAction,
        name: this.title,
        company_cd: "",
        company_nm: "",
        company_mng_nm: "",
        mng_tel: "",
        mail: "",
        bank: "",
        account: "",
        zip_code: "",
        join_date: "",
        addr: "",
        addr_detail: "",
        delshow: true,
        active: "",
      };
    },
    mounted: function () {
      console.log("Mounted. loginID:", this.loginID, "action:", this.action);
      let vm = this;
      if (this.loginID == null || this.loginID == "") {
        vm.loginID = "";
        vm.company_cd = "";
        vm.company_nm = "";
        vm.company_mng_nm = "";
        vm.mng_tel = "";
        vm.mail = "";
        vm.bank = "";
        vm.account = "";
        vm.zip_code = "";
        vm.join_date = "";
        vm.addr = "";
        vm.addr_detail = "";
        vm.active = "";
      } else {
        // 수정시 해당하는 상세정보 가져옴
        let params = new URLSearchParams();
        params.append("loginID", this.loginID);
  
        this.axios
          .post("/scm/vueCusDetailInfo.do", params)
          .then(function (response) {
            //console.log("API 응답확인", JSON.stringify(response)); //데이터구조를 확인한 후에 볼수 있다.
  
            vm.loginID = response.data.cusDetailInfo[0].loginID;
            vm.company_cd = response.data.cusDetailInfo[0].company_cd;
            vm.company_nm = response.data.cusDetailInfo[0].company_nm;
            vm.company_mng_nm = response.data.cusDetailInfo[0].company_mng_nm;
            vm.mng_tel = response.data.cusDetailInfo[0].mng_tel;
            vm.mail = response.data.cusDetailInfo[0].mail;
            vm.bank = response.data.cusDetailInfo[0].bank;
            vm.account = response.data.cusDetailInfo[0].account;
            vm.zip_code = response.data.cusDetailInfo[0].zip_code;
            vm.join_date = response.data.cusDetailInfo[0].join_date;
            vm.addr = response.data.cusDetailInfo[0].addr;
            vm.addr_detail = response.data.cusDetailInfo[0].addr_detail;
            vm.active = response.data.cusDetailInfo[0].active;
          })
          .catch(function (error) {
            console.error("API 호출 중 에러:", error);
            alert("에러! API요청에 오류가 있습니다" + error);
          });
      }
    },
  
    methods: {
      cancel: function () {
        let vm = this;
        closeModal(vm);
      },
      save: function () {
        if (confirm("저장하시겠습니까?")) {
          let vm = this;
  
          const formData = new FormData();
          formData.append("loginID", this.loginID);
          formData.append("company_cd", this.company_cd);
          formData.append("company_nm", this.company_nm);
          formData.append("company_mng_nm", this.company_mng_nm);
          formData.append("mng_tel", this.mng_tel);
          formData.append("mail", this.mail);
          formData.append("bank", this.bank);
          formData.append("account", this.account);
          formData.append("zip_code", this.zip_code);
          formData.append("addr", this.addr);
          formData.append("addr_detail", this.addr_detail);
          formData.append("active", this.active);
  
           if(this.action == "I") {
            this.saveUrl = "/scm/sendCusActiveSave.do"
          } else if(this.action == "U") {
            formData.append("loginID", this.loginID);
            this.saveUrl = "/scm/sendCusActiveModify.do"
          }
  
          console.log("URL:", this.saveUrl);
          console.log("action:", this.action);
        
          console.log("FormData:", formData);
  
          this.axios({
              method: "post",
              url: this.saveUrl,
              data: formData
          })
            .then(function (response) {
              
  
              console.log(response);
              console.log("Login ID: 값이 나옴?", vm.loginID);
              alert("저장이 완료되었습니다.");
              closeModal(vm);
            })
            .catch(function (error) {
              alert("저장에 실패하였습니다." + error);
            });
        }
      },
      deleteCusinfo: function() {
        if(confirm("삭제하시겠습니까?")){
           let vm = this; 
           let params = new URLSearchParams();
           params.append("loginID", this.loginID);
  
           this.axios
            .post("/scm/deleteCusinfoVue.do", params)
            .then(function(response){
              alert(response.data.resultMsg);
              closeModal(vm);
            })
            .catch(function (error){
              alert("에러입니다. API요청에 오류가 있습니다. " + error);
            });
        }
      },
    },
  };
  </script>
  <style>
  </style>
  