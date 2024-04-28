## 기업 고객 관리 페이지 (src/views/scm/cusinfo.vue)

![image](https://github.com/NayoungKim1212/vue_scm/assets/132897437/363549d3-97fc-47ef-94a5-70c4a4c5e070)


- 기업고객관리 리스트 불러오기
  
    - v-for 디렉티브를 사용하여 listitem 배열의 각 아이템에 대해 테이블 행 생성

- 검색 기능
  
    - v-if와 v-else를 사용하여 검색 조건에 따라 렌더링
      
    - 사용자가 드롭다운 목록에서 옵션을 선택하면, v-model에 의해  searchKey라는 데이터 속성이 선택된 옵션의 value로 업데이트
      
    - searchKey 값이 변경되면, Vue 인스턴스 내에서 이 값에 의존하는 다른 데이터나 메소드도 자동으로 갱신됨

- 검색 버튼과 신규 등록 버튼
  
    - 이벤트 핸들러 v-on:click을 사용하여 클릭 시 메소드 호출
    
- 페이지네이션 기능을 구현
  
    - paginate 컴포넌트를 사용
    
- 비활성화된 항목 표시
  
    - v-model을 활용하여, 체크박스의 상태를 Vue 인스턴스의 searchUseYn라는 데이터 속성과 동기화
      
    - 체크박스가 체크되거나 해제될 때마다 searchUseYn의 값을 업데이트
      
    - 변경 이벤트 핸들러(@change)를 사용하여 사용자가 체크박스의 상태를 변경할 때마다 리스트를 불러오는 메소드 호출

### 모달 (src/components/scm/cusinfoDetailModal.vue)

![image](https://github.com/NayoungKim1212/vue_scm/assets/132897437/b59c6a06-aa87-4755-a198-b8f30aa9ffa5)


- v-model을 사용하여 해당 Vue 인스턴스의 데이터 속성)과 입력 필드를 양방향으로 연결하여 데이터를 실시간으로 동기화
  
- 주소와 상세 주소
  
    - readOnly로 지정해 사용자가 직접 수정할 수 없고, 주소 검색 API를 통해 자동으로 채워지게 구현
      
- 우편번호
  
    - v-if를 사용하여  paction의 값에 따라 다른 내용을 보여줌
      
    - 등록에서는 "우편번호 찾기" 버튼을 활성화
      
    - 수정에서는 "우편번호 찾기" 버튼을 비활성화 하고 가입 일자 입력 필드 추가
