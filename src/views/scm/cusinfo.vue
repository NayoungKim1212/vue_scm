<template>
  <div id="cusInfo">
    <p class="Location">
      <a href="/dashboard/home" class="btn_set home"></a>
      <span class="btn_nav bold">기준정보</span>
      <span class="btn_nav bold">기업고객 관리</span>
      <a href="../scm/cusinfo.do" class="btn_set refresh">새로고침</a>
    </p>
    <p class="conTitle">
      <span>기업고객관리</span>
      <span>
        <table style="collapse; border: 1px #50bcdf;" width="100%" cellpadding="5" cellspacing="0" border="1"
          align="left">
          <tr style="border: 0px; border-color: blue">
            <td width="50" height="25" style="font-size: 100%; text-align: left;">
              <div id="searchArea" class="d-flex justify-content-around">
                <select id="searchKey" class="form-control" style="width: 150px;" v-model="searchKey">
                  <option value="all">전체</option>
                  <option value="company_nm">회사명</option>
                  <option value="company_mng_nm">담당자명</option>
                </select>

                <input type="text" class="form-control" style="width: 200px;" v-model="searchInfo">

                <span class="fr">
                  <a @click="searchButton" class="btn btn-primary mx-2" id="search_button" name="btn">
                    <span>검 색</span>
                  </a>
                  <a class="btn btn-primary mx-2" @click="searchDetail('')" name="modal">
                    <span>신규등록</span>
                  </a>

                  <input type="checkbox" id="searchUseYn" name="searchUseYn" :true-value="'N'" :false-value="'Y'"
                    v-model="searchUseYn" @change="searchList">
                  <label for="searchUseYn" style="display: inline-block; margin-top: 2px;">비활성화된
                    항목 표시</label>
                </span>

              </div>
            </td>
          </tr>
        </table>
      </span>
    </p>
    <div class="divCusInfoList">
      <table class="col" style="margin-top: 15px;">
        <caption>
          caption
        </caption>
        <colgroup>
          <col width="12%">
          <col width="12%">
          <col width="12%">
          <col width="12%">
        </colgroup>

        <thead>
          <tr>
            <th scope="col">회사명</th>
            <th scope="col">담당자명</th>
            <th scope="col">연락처</th>
            <th scope="col">활성화여부</th>
          </tr>
        </thead>
        <tbody>
          <template v-if="totalCnt == 0">
            <tr>
              <td colspan="10">일치하는 검색 결과가 없습니다</td>
            </tr>
          </template>

          <template v-else>
            <tr v-for="item in listitem" :key="item.loginID">
              <td style="cursor: pointer;">
                <a @click="searchDetail(item.loginID)"><span>{{ item.company_nm }}</span></a>
              </td>
              <td>{{ item.company_mng_nm }}</td>
              <td>{{ item.mng_tel }}</td>
              <td><input type="hidden" id="activeStatus" value="${list.active}" />{{ item.active }}</td>
            </tr>
          </template>
        </tbody>
      </table>
    </div>
    <div id="cusInfoPagination">
      <paginate class="justify-content-center" v-model="currentPage" :page-count="totalPage" :page-range="5"
        :margin-pages="0" :click-handler="clickCallback" :prev-text="'<'" :next-text="'>'"
        :container-class="'pagination'" :page-class="'page-item'" :first-last-button=true :first-button-text="'<<'"
        :last-button-text="'>>'">
      </paginate>
    </div>
  </div>
</template>

<script>
import Paginate from "vuejs-paginate-next";
import cusinfoDetailModal from "@/components/scm/cusinfoDetailModal.vue";
import { openModal } from "jenesius-vue-modal";

export default {
  data: function () {
    return {
      pageSize: 10,
      pageBlockSize: 5,
      currentPage: 1,
      listitem: [],
      option: '',
      cusListCnt: 0,
      searchKey: 'all',
      searchUseYn: false,
      totalPage: 1,
      totalCnt: 0,
      searchInfo: '',
      action: '',
      loginID: "",
      pagehtml: "",
    };
  },
  components: {
    paginate: Paginate,
  },
  watch: {
    searchUseYn(newValue) {
      console.log("newValue : " + newValue);
      this.searchList();
    }
  },
  mounted() {
    this.searchButton();
  },
  methods: {
    searchDetail: async function (loginID) {

      if (!loginID) {
        this.action = "I";

        const modal = await openModal(cusinfoDetailModal, {
          title: "기업고객 등록",
          loginId: loginID,
          action: this.action,
        });
        modal.onclose = (popupparam) => {
          console.log("Close : " + popupparam);
          this.searchList();
          //return false; //Modal will not be closed
        };
      } else {
        console.log("여긴cusinfo::: " + loginID);

        this.action = "U";
        const modal = await openModal(cusinfoDetailModal, {
          title: "상세정보",
          loginId: loginID,
          action: this.action,
        });

        modal.onclose = (popupparam) => {
          console.log("Close : " + popupparam);
          this.searchList();
          //return false; //Modal will not be closed
        };
      }
    },
    searchButton: function () {
      this.currentPage = 1;
      this.searchList();
    },

    searchList: function () {
      let vm = this;
      let params = new URLSearchParams();
      params.append("currentPage", this.currentPage.toString());
      params.append("pageSize", this.pageSize.toString());
      params.append("searchKey", this.searchKey);
      params.append("searchInfo", this.searchInfo);

      // Append "showY" parameter based on the state of "searchUseYn".
      // If the checkbox is unchecked, "searchUseYn" will be 'Y' (active), 
      // and if checked, it will be 'N' (inactive).
      params.append("showY", this.searchUseYn);

      console.log("option: ", this.searchKey, "searchInfo: ", this.searchInfo);
      console.log("searchUseYn", this.searchUseYn);

      this.axios
        .post("/scm/cusListInfo.do", params)
        .then((response) => {
          this.totalCnt = response.data.cusListCnt;
          this.listitem = response.data.cusList;
          this.totalPage = this.page();
        })
        .catch((error) => {
          alert("에러! API 요청에 오류가 있습니다. " + error);
        });
    },
    clickCallback: function (pageNum) {
      console.log(pageNum);

      this.currentPage = pageNum;
      //this.Paginate.pageNum = 10;
      this.searchList();
    },
    page: function () {
      var total = this.totalCnt;
      var page = this.pageSize;
      var xx = total % page;
      var result = parseInt(total / page);

      if (xx == 0) {
        return result;
      } else {
        result = result + 1;
        return result;
      }
    }
  },
}
</script>

<style>
#searchArea {
  margin-top: 25px;
  margin-bottom: 25px;
}

#searchArea>* {
  height: auto;
}

#groupTitle {
  text-decoration: underline;
  font-weight: bold;
  cursor: pointer;
}
</style>