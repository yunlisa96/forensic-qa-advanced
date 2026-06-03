# forensic-qa-advanced

## 프로젝트 개요
실제 NIST 공개 포렌식 이미지(SCHARDT)를 Autopsy로 분석하고,
분석 결과를 Python pytest로 교차 검증하는 자동화 테스트 프로젝트입니다.

단순 자동화 테스트를 넘어 **실제 포렌식 툴(Autopsy)의 분석 결과가
올바른지 독립적으로 검증**하는 QA 프로세스를 구현했습니다.

## 기술 스택
- Python 3.9
- pytest / pytest-html
- Autopsy 4.23.1
- NIST CFReDS Hacking Case (SCHARDT 디스크 이미지)

## 프로젝트 구조

```
autopsy-forensic-qa/
├── tests/
│   └── test_hash_verification.py  ← Autopsy 교차 검증 테스트
├── test_cases/
│   ├── TC_hash_verification.md    ← 해시 검증 TC 문서
│   ├── TC_file_recovery.md        ← 파일 복구 TC 문서
│   └── TC_metadata.md             ← 메타데이터 TC 문서
├── autopsy_results/               ← Autopsy 분석 스크린샷
└── reports/                       ← 테스트 결과 리포트
```

## 테스트 데이터
- **NIST CFReDS Hacking Case** - 2004년 실제 해킹 수사 케이스
- Dell CPi 노트북 디스크 이미지 (4.6GB, 8개 분할 파일)
- 용의자 계정: Mr. Evil
- 웹 기록 887개, 삭제 파일 365개 포함

## 테스트 구성

### 해시 무결성 교차 검증 (`test_hash_verification.py`)
Autopsy가 계산한 해시값과 Python이 독립적으로 계산한 해시값을 비교합니다.

| 테스트 | 목적 |
|---|---|
| test_full_image_md5_matches_autopsy | 전체 이미지 MD5가 NIST 공식값과 일치하는지 검증 |
| test_full_image_sha1_matches_autopsy | 전체 이미지 SHA1이 NIST 공식값과 일치하는지 검증 |
| test_full_image_sha256_matches_autopsy | 전체 이미지 SHA256이 NIST 공식값과 일치하는지 검증 |
| test_partial_image_differs_from_full | 분할 파일 일부만 계산 시 전체와 다른지 검증 |
| test_file_order_matters | 파일 순서가 바뀌면 해시값도 달라지는지 검증 |

## 검증 결과

| 알고리즘 | NIST 공식값 | Python 계산값 | 일치 여부 |
|---|---|---|---|
| MD5 | aee4fcd9... | aee4fcd9... | ✅ 일치 |
| SHA1 | 4dbbeb5b... | 4dbbeb5b... | ✅ 일치 |
| SHA256 | a82fb41b... | a82fb41b... | ✅ 일치 |

## 실행 방법

```bash
# 가상환경 활성화
source venv/bin/activate

# 테스트 실행
pytest tests/ -v

# HTML 리포트 생성
pytest tests/ -v --html=reports/report.html --self-contained-html
```


- MD-RED의 Data Source Integrity 기능 검증 방식 적용
- 실제 포렌식 툴과 자동화 테스트 결과 교차 검증
- 수동 TC 문서 + 자동화 테스트 병행
