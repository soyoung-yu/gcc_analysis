# 법인별 표준 데이터마트 설명 (NPD 의뢰)

> 이 문서는 `./manual_to_standard_data/` 폴더에 저장된 법인별 표준 데이터마트 CSV 파일에 대한 설명입니다.

---

## 1. 개요

### 파일 생성 목적
법인별로 상이한 포맷의 수작업 엑셀 파일을 하나의 공통 표준 컬럼 구조로 변환하여, 이후 시스템 DB 데이터와의 매핑 및 통합 분석이 가능하도록 준비하는 중간 산출물입니다.

### 생성 스크립트
`manual_to_standard_datamart.ipynb`

### 파일 목록

| 파일명 | 법인 | comp_id | 최종 행 수 |
|---|---|---|---|
| `상해_standard.csv` | 상해 | 5100 | 3,434 |
| `광저우_standard.csv` | 광저우 | 5200 | 6,175 |
| `미국_standard.csv` | 미국 | 6400 | 386 |
| `태국_standard.csv` | 태국 | 7200 | 888 |
| `인니_standard.csv` | 인도네시아 | 7100 | 1,095 |

### 생성 기준

#### 날짜 기준
- `request_date` 기준 **2026-01-01 이후** 데이터만 포함
- 날짜 파싱이 불가한 값(NaT)은 제외

#### 전처리 기준
- **전체 NaN 행 제거**: 모든 표준 컬럼이 빈 값인 행 제거
- **컬럼명 정규화**: 수작업 컬럼명의 `\n` 및 모든 공백을 제거한 후 표준 컬럼명과 매핑
- **복수 컬럼 합산**: 복수의 수작업 컬럼을 `|` 구분자로 이어붙여 하나의 표준 컬럼에 저장
  - 미국: `Entity`, `Status`, `OTC(Sunscreen)` → `memo`
  - 인니: `Formulation`, `New/existing` → `memo`
- **혼합 사용 컬럼 분리**: 1개 수작업 컬럼이 2개 표준 컬럼에 걸쳐 사용되는 경우 별도 처리
  - 상해 `제품명` → `product_name` + `bulk_name` 양쪽에 동일 값 복사
  - 태국 `NPD Code` → prefix `P9`이면 `product_code`, prefix `P3`이면 `bulk_code`로 분리
  - 태국 `Product Name` → `product_name` + `bulk_name` 양쪽에 동일 값 복사

---

## 2. 표준 컬럼 설명

> `product_code`와 `bulk_code`는 이후 시스템 DB와의 매핑에 사용되는 핵심 컬럼입니다.

| 컬럼명 | 설명 |
|---|---|
| `req_type_nm` | 의뢰 접수 타입 |
| `dev_comp_id` | 개발법인 코드 |
| `req_nation_cd` | 의뢰법인 코드 |
| `request_date` | 의뢰 날짜 |
| `gcc_category` | GCC(Global Control Center) 카테고리 대분류 |
| `proc_status` | 의뢰 상태 |
| ⭐ `product_code` | 신제품접수코드 — 시스템 DB 매핑 핵심 컬럼 |
| ⭐ `bulk_code` | 벌크구분코드 — 시스템 DB 매핑 핵심 컬럼 |
| `mitem` | 벌크코드 (SAP) |
| `item2` | 완제품코드 (SAP) |
| `forml_code` | 소제형코드 |
| `product_name` | 접수 제품명 (신제품접수코드 이름) |
| `bulk_name` | 접수 벌크명 (벌크구분코드 이름) |
| `customer_name` | 고객사명 |
| `brand_name` | 브랜드명 |
| `last_labno` | 최종 랩넘버 |
| `requester_id` | 의뢰법인 담당연구원 사번 |
| `requester_name` | 의뢰법인 담당연구원명 |
| `req_teamnm` | 의뢰법인 담당연구원 팀명 |
| `developer_id` | 개발법인 담당연구원 사번 |
| `developer_name` | 개발법인 담당연구원명 |
| `dev_teamnm` | 개발법인 담당연구원 팀명 |
| `reg_id` | 의뢰법인 등록자 ID |
| `reg_nm` | 의뢰법인 등록자명 |
| `reg_deptid` | 의뢰법인 등록자 팀 ID |
| `reg_team` | 의뢰법인 등록자 팀 |
| `req_id` | 의뢰법인 마케팅 담당자 ID |
| `req_nm` | 의뢰법인 마케팅 담당자명 |
| `req_team` | 의뢰법인 마케팅 팀 |
| `req_num` | 의뢰번호 |
| `del_yn` | 삭제여부 |
| `memo` | 비고 |

---

## 3. 법인별 데이터 현황

### 상해 (`상해_standard.csv` / comp_id: 5100)

| 항목 | 값 |
|---|---|
| 원본 행 수 | 23,210 |
| 전체 NaN 행 제거 후 | 23,210 |
| 2026년 이후 (최종) | **3,434** |

**매핑된 컬럼**: `req_type_nm`, `request_date`, `gcc_category`, `proc_status`, `product_code`, `bulk_code`, `mitem`, `item2`, `forml_code`, `product_name`(제품명 복사), `bulk_name`(제품명 복사), `customer_name`, `brand_name`, `requester_name`, `req_teamnm`, `developer_name`, `dev_teamnm`, `req_nm`, `req_team`, `del_yn`, `memo`

**항상 빈 값인 컬럼**: `dev_comp_id`, `req_nation_cd`, `last_labno`, `requester_id`, `developer_id`, `reg_id`, `reg_nm`, `reg_deptid`, `reg_team`, `req_id`, `req_num`

---

### 광저우 (`광저우_standard.csv` / comp_id: 5200)

| 항목 | 값 |
|---|---|
| 원본 행 수 | 52,188 |
| 전체 NaN 행 제거 후 | 52,180 |
| 2026년 이후 (최종) | **6,175** |

**매핑된 컬럼**: `req_type_nm`, `request_date`, `gcc_category`, `proc_status`, `product_code`, `bulk_code`, `mitem`, `item2`, `forml_code`, `product_name`, `bulk_name`, `customer_name`, `brand_name`, `last_labno`, `requester_name`, `req_teamnm`, `developer_name`, `dev_teamnm`, `req_nm`, `req_team`, `del_yn`, `memo`

**항상 빈 값인 컬럼**: `dev_comp_id`, `req_nation_cd`, `requester_id`, `developer_id`, `reg_id`, `reg_nm`, `reg_deptid`, `reg_team`, `req_id`, `req_num`

---

### 미국 (`미국_standard.csv` / comp_id: 6400)

| 항목 | 값 |
|---|---|
| 원본 행 수 | 3,292 |
| 전체 NaN 행 제거 후 | 3,292 |
| 2026년 이후 (최종) | **386** |

**매핑된 컬럼**: `request_date`, `gcc_category`, `product_code`, `bulk_code`, `forml_code`, `product_name`, `bulk_name`, `customer_name`, `brand_name`, `developer_name`, `dev_teamnm`, `req_nm`, `memo`(Entity \| Status \| OTC(Sunscreen) 합산)

**항상 빈 값인 컬럼**: `req_type_nm`, `dev_comp_id`, `req_nation_cd`, `mitem`, `item2`, `last_labno`, `requester_id`, `requester_name`, `req_teamnm`, `developer_id`, `reg_id`, `reg_nm`, `reg_deptid`, `reg_team`, `req_id`, `req_team`, `req_num`, `del_yn`, `proc_status`

---

### 태국 (`태국_standard.csv` / comp_id: 7200)

| 항목 | 값 |
|---|---|
| 원본 행 수 | 5,888 |
| 전체 NaN 행 제거 후 | 5,888 |
| 2026년 이후 (최종) | **888** |

**매핑된 컬럼**: `request_date`, `gcc_category`, `product_code`(NPD Code P9 prefix), `bulk_code`(NPD Code P3 prefix), `product_name`(Product Name 복사), `bulk_name`(Product Name 복사), `customer_name`, `brand_name`, `req_nm`, `memo`

**항상 빈 값인 컬럼**: `req_type_nm`, `dev_comp_id`, `req_nation_cd`, `proc_status`, `mitem`, `item2`, `forml_code`, `last_labno`, `requester_id`, `requester_name`, `req_teamnm`, `developer_id`, `developer_name`, `dev_teamnm`, `reg_id`, `reg_nm`, `reg_deptid`, `reg_team`, `req_id`, `req_team`, `req_num`, `del_yn`

---

### 인도네시아 (`인니_standard.csv` / comp_id: 7100)

| 항목 | 값 |
|---|---|
| 원본 행 수 | 2,302 |
| 전체 NaN 행 제거 후 | 2,301 |
| 2026년 이후 (최종) | **1,095** |

**매핑된 컬럼**: `req_type_nm`, `request_date`, `gcc_category`, `product_code`, `bulk_code`, `bulk_name`, `customer_name`, `brand_name`, `req_nm`, `req_team`, `memo`(Formulation \| New/existing 합산)

**항상 빈 값인 컬럼**: `dev_comp_id`, `req_nation_cd`, `proc_status`, `mitem`, `item2`, `forml_code`, `product_name`, `last_labno`, `requester_id`, `requester_name`, `req_teamnm`, `developer_id`, `developer_name`, `dev_teamnm`, `reg_id`, `reg_nm`, `reg_deptid`, `reg_team`, `req_id`, `req_num`, `del_yn`

---

## 4. 데이터 품질 이슈

### `product_code` & `bulk_code` 동시 누락 현황

시스템 DB 매핑의 핵심 컬럼인 `product_code`와 `bulk_code`가 둘 다 없는 행은 매핑이 불가한 미식별 의뢰 건입니다.

| 법인 | 누락 건수 | 전체 대비 비율 |
|---|---|---|
| 상해 | 0건 | 0.0% |
| 광저우 | 0건 | 0.0% |
| 미국 | 61건 | 15.8% |
| 태국 | 17건 | 1.9% |
| 인니 | 0건 | 0.0% |

#### 미국 월별 누락 현황 (주의)

| 월 | 누락 건수 | 월 전체 건수 | 비율 |
|---|---|---|---|
| 2026-01 | 19건 | 118건 | 16.1% |
| 2026-02 | 13건 | 48건 | 27.1% |
| 2026-03 | 29건 | 70건 | 41.4% |

> 미국은 3월 누락률이 41.4%로 급증하고 있어 원인 파악이 필요합니다.

#### 태국 월별 누락 현황

| 월 | 누락 건수 | 월 전체 건수 | 비율 |
|---|---|---|---|
| 2026-01 | 4건 | 174건 | 2.3% |
| 2026-03 | 4건 | 374건 | 1.1% |
| 2026-04 | 3건 | 144건 | 2.1% |
| 2026-05 | 6건 | 151건 | 4.0% |

### 공통 미입력 컬럼

아래 컬럼은 모든 법인의 수작업 데이터에 해당 정보가 없어 전체 빈 값입니다. 시스템 DB 연동 후 채울 예정입니다.

`dev_comp_id`, `req_nation_cd`, `requester_id`, `developer_id`, `reg_id`, `reg_nm`, `reg_deptid`, `reg_team`, `req_id`, `req_num`
