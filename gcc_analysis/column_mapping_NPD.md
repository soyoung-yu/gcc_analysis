# 법인별 수작업 데이터 컬럼 매핑 (NPD 의뢰)

> 시스템DB 컬럼과 법인별 수작업 엑셀 컬럼 간의 매핑 정의.
> 동일한 수작업 컬럼명이 여러 시스템DB 컬럼에 매핑된 경우, 해당 수작업 컬럼은 여러 시스템 내 컬럼이 혼합되어 사용되었을 가능성이 있음.

---

## 매핑 테이블

| 시스템DB 컬럼 | 설명 | 상해 | 광저우 | 미국 | 태국 | 인니 |
|---|---|---|---|---|---|---|
| `req_type_nm` | 의뢰 접수 타입 | 구분 | 구분 | - | - | Classification |
| `dev_comp_id` | 개발법인 코드 | - | - | - | - | - |
| `req_nation_cd` | 의뢰법인 코드 | - | - | - | - | - |
| `request_date` | 의뢰 날짜 | 의뢰일자 | 의뢰일자 | Request Date | NPS Date | Request date |
| `gcc_category` | GCC 카테고리 대분류 | 카테고리 | 扩大 | Category(2308) | 회의용 분류 | Extended Category |
| `proc_status` | 의뢰 상태 | 상태 | 상태 | - | - | - |
| `product_code` | 신제품접수코드 | 신제품접수코드 | 신제품접수코드 | NPD Product No | NPD Code | NPD Product No |
| `bulk_cod` | 벌크구분코드 | 벌크구분코드 | 벌크구분코드 | NPD Bulk No | NPD Code | NPD Bulk No |
| `mitem` | 벌크코드 | SAP 벌크코드 | SAP 벌크코드 | - | - | - |
| `item2` | 완제품코드 | SAP 제품코드 | SAP 제품코드 | - | - | - |
| `forml_code` | 소제형코드 | 제형코드 | 제형코드 | Small class code | - | - |
| `product_name` | 접수 제품명 (신제품접수코드 이름) | 제품명 | 제품명 | Product Name | Product Name | - |
| `bulk_name` | 접수 벌크명 (벌크구분코드 이름) | 제품명 | 벌크명 | Bulk Name | Product Name | Bulk name |
| `customer_name` | 고객사명 | 고객사명 | 고객사명 | Customer | Customer Name | Customer |
| `brand_name` | 브랜드명 | 브랜드명 | 브랜드명 | Brand | Brand | Brand |
| `last_labno` | 최종 랩넘버 | - | 랩넘버 | - | - | - |
| `requester_id` | 의뢰법인 담당연구원 사번 | - | - | - | - | - |
| `requester_name` | 의뢰법인 담당연구원명 | (의뢰법인)제형 연구원 | (의뢰법인)제형연구원 | - | - | - |
| `req_teamnm` | 의뢰법인 담당연구원 팀명 | (의뢰법인)제형팀 | (의뢰법인)제형팀 | - | - | - |
| `developer_id` | 개발법인 담당연구원 사번 | - | - | - | - | - |
| `developer_name` | 개발법인 담당연구원명 | (개발법인)제형연구원 | (개발법인)제형연구원 | Chemist | - | - |
| `dev_teamnm` | 개발법인 담당연구원 팀명 | (개발법인)제형팀 | (개발법인)제형팀 | Team | - | - |
| `reg_id` | 의뢰법인 등록자 ID | - | - | - | - | - |
| `reg_nm` | 의뢰법인 등록자명 | - | - | - | - | - |
| `reg_deptid` | 의뢰법인 등록자 팀 ID | - | - | - | - | - |
| `reg_team` | 의뢰법인 등록자 팀 | - | - | - | - | - |
| `req_id` | 의뢰법인 마케팅 담당자 ID | - | - | - | - | - |
| `req_nm` | 의뢰법인 마케팅 담당자명 | 마케팅담당자 | 마케팅담당자 | Sales name | Person in charge (MK) | Sales In Charge |
| `req_team` | 의뢰법인 마케팅 팀 | 마케팅팀 | 마케팅팀 | - | - | Sales team |
| `req_num` | 의뢰번호 | - | - | - | - | - |
| `del_yn` | 삭제여부 | 삭제여부 | 삭제여부 | - | - | - |
| `memo` | 비고 | 비고(是否储备品) | Sample/Benchmark/Lab.# | Entity, Status, OTC(Sunscreen) | Sub Category | Formulation, New/existing |

> `-` : 해당 법인 수작업 파일에 매핑 컬럼 없음 (시스템DB에서 직접 조회하거나 수작업 데이터에 미포함)
> 셀 값이 쉼표로 구분된 복수 컬럼명인 경우, 해당 컬럼들을 `|` 구분자로 이어붙여 하나의 표준 컬럼에 저장함

---

## 혼합 사용 주의 컬럼

동일한 수작업 컬럼명이 여러 시스템DB 컬럼에 매핑된 경우, 해당 수작업 컬럼에는 복수의 시스템 컬럼 데이터가 혼합되어 입력되었을 가능성이 있음.

| 법인 | 수작업 컬럼명 | 매핑된 시스템DB 컬럼 | 비고 |
|---|---|---|---|
| 태국 | `NPD Code` | `product_code` (신제품접수코드), `bulk_cod` (벌크구분코드) | 신제품접수코드(P9%)와 벌크구분코드(P3%)가 혼용 기입되고 있음. Prefix로 구분 필요. |
| 상해 | `제품명` | `product_name` (제품명), `bulk_name` (벌크명) | 제품명과 벌크명이 동일 컬럼에 혼용될 가능성 있음 |
| 태국 | `Product Name` | `product_name` (제품명), `bulk_name` (벌크명) | 제품명과 벌크명이 동일 컬럼에 혼용될 가능성 있음 |

---

## 비고 컬럼 (`memo`) 법인별 활용 내용

`memo` 컬럼은 법인마다 기입 내용이 다르며, 참고용으로만 활용.
복수 컬럼을 합산하는 경우 `|` 구분자로 이어붙임. (예: `"A값 | B값 | C값"`)

| 법인 | 수작업 컬럼명 | 합산 여부 | 주요 기입 내용 |
|---|---|---|---|
| 상해 | `비고(是否储备品)` | 단일 컬럼 | 자유 기입 비고 |
| 광저우 | `Sample/Benchmark/Lab.#` | 단일 컬럼 | 샘플, 벤치마크, 랩넘버 정보 |
| 미국 | `Entity`, `Status`, `OTC(Sunscreen)` | **3개 컬럼 합산** | 의뢰 유형 구분, 상태, OTC(선크림 해당 여부) |
| 태국 | `Sub Category` | 단일 컬럼 | 서브 카테고리 |
| 인니 | `Formulation`, `New/existing` | **2개 컬럼 합산** | 제형 정보, 신규/기존 여부 |

