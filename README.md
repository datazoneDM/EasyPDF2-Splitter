# EasyPDF2 - PDF Split & Encryption Tool

대용량 통파일 PDF를 개별 고객 단위로 분할(Split)하고, 고객별 고유 패스워드를 부여하여 독립된 PDF로 생성하는 자동화 프로그램입니다.

## 🛠 주요 기능 및 특징
* **PDF 분할:** `iText 7`의 `PdfSplitter`를 활용하여 지정된 페이지 구간별로 PDF를 정밀하게 분할합니다.
* **보안 암호화:** 분할된 개별 파일에 AES-256 기반의 암호화(Password Encryption)를 적용합니다.
* **정합성 검증:** 분할 전후의 특정 영역 텍스트(POSTID 등)를 추출(`PdfTextExtractor`)하여 파일이 정확히 분할되었는지 검증합니다.
* **환경:** Microsoft Visual Studio / C# .NET

## ⚖️ 라이선스 및 컴플라이언스 (License & Compliance)

본 프로젝트는 오픈소스 라이브러리인 iText 7을 사용하고 있으며, 이에 따라 전체 소스 코드는 **GNU Affero General Public License v3.0 (AGPL-3.0)**에 의거하여 배포됩니다.

> **[Compliance Notice]**
> This project is used as part of a business system for PDF processing. The source code is provided strictly to comply with the AGPL-3.0 license requirements.
> (본 프로젝트는 비즈니스 시스템의 PDF 처리 모듈로 사용되며, 본 소스 코드는 AGPL-3.0 라이선스의 소스코드 공개 의무를 완벽히 준수하기 위한 목적으로 투명하게 공개되었습니다.)

**오픈소스 준수 및 고지사항:**
1. 본 프로그램의 코드를 복사, 수정, 배포하거나 네트워크 서비스 형태로 제공하는 경우, 해당 시스템의 소스 코드 역시 AGPLv3 조건에 따라 공개해야 할 의무가 발생할 수 있습니다.
2. 기업 환경 등 소스 코드 공개를 원치 않는 상업적 용도로 본 프로그램을 활용하고자 할 경우, iText 공식 홈페이지를 통해 상업용 라이선스(Commercial License)를 별도로 취득하시기 바랍니다.

* **Source Code Repository:** [https://github.com/datazoneDM/EasyPDF2-Splitter](https://github.com/datazoneDM/EasyPDF2-Splitter)

---

### License Notice

This product includes software developed by iText Software.

* **Library:** iText 7
* **License:** GNU Affero General Public License v3 (AGPLv3)
* **More Info:** https://itextpdf.com

If you use this software over a network or distribute it, you must provide access to the complete corresponding source code as required by the AGPL license.

---

## 시스템 구조 및 독립 실행 방식

본 프로그램(`EasyPDF`)은 외부 상용 솔루션이나 타 시스템과 라이브러리 링크(DLL), 메모리 공유, API/DB 연동을 수행하지 않는 **단독 실행형(Standalone) 유틸리티**입니다. 로컬 파일 시스템에 저장된 표준 PDF 파일과 일반 텍스트 파일(`.cfg`, `.inf`)만을 읽어 처리한 뒤 지정된 폴더로 결과 파일을 복사하고 종료합니다.

```text
[외부 PDF 생성 도구]
   │ (표준 .pdf 및 텍스트 .inf 파일 생성 — 직접 연동/링크 없음)
   ▼
[로컬 파일 시스템 (입력 폴더)]
   │ (파일 읽기)
   ▼
[EasyPDF.exe (AGPLv3 독립 유틸리티)]
   │ (페이지 분할, 비밀번호 암호화 후 결과 파일 복사)
   ▼
[로컬/지정 출력 폴더]
```

## 빌드 및 실행 방법

1. Visual Studio에서 프로젝트(`.sln` / `.csproj`)를 엽니다.
2. NuGet 패키지(`iText 7` 관련 라이브러리)를 복원한 뒤 솔루션을 빌드하여 `easyPDF.exe`를 생성합니다.
3. 아래 안내에 따라 설정 파일(`EasyPDF.cfg`)과 입력 파일(`.pdf`, `.inf`)을 준비한 후 `easyPDF.exe`를 실행합니다.

---

## 설정 및 입력 파일 포맷 안내

### 1. 실행 설정 파일 (`EasyPDF.cfg`)
`Program.cs`에 정의된 기본 경로(`C:\Program Files\Quadient\easyPDF\EasyPDF.cfg`) 또는 소스코드에서 지정한 경로에 **`Key:Value`** 형식의 일반 텍스트 파일(**CP949 인코딩**)을 배치합니다.
*(주의: 경로 항목은 반드시 끝을 백슬래시 `\`로 마무리해야 합니다.)*

```text
pdf입력경로:C:\Sample\Input\
그룹01:N
그룹02:N
그룹03:N
그룹04:N
그룹05:N
그룹06:N
그룹07:N
그룹08:N
그룹09:N
그룹10:N
개발계컴퓨터:DEV_PC_NAME
파일이동개발:C:\Sample\Output\Dev\
파일이동운영:C:\Sample\Output\Prod\
```

* `개발계컴퓨터`: 현재 실행 중인 PC의 호스트명(`SystemInformation.ComputerName`)과 비교하여 일치하면 `파일이동개발` 경로로, 일치하지 않으면 `파일이동운영` 경로로 결과 파일을 복사합니다.

### 2. PDF 분할 메타데이터 파일 (`*.inf`)
`pdf입력경로` 하위의 **`M\`** 또는 **`E\`** 폴더에 대상 PDF 파일과 동일한 파일명(예: `sample.pdf` / `sample.inf`)으로 파이프(`|`) 구분자 **19개 컬럼** 형식의 일반 텍스트 파일(**CP949 인코딩**)을 배치합니다.

* **컬럼 순서 (총 19개):**
  `일련번호|시작페이지|종료페이지|패스워드|파일명|채널구분|그룹01|그룹02|그룹03|그룹04|그룹05|그룹06|그룹07|그룹08|그룹09|그룹10|infoid|postid|출력여부`

* **입력 파일 예시 (`sample.inf`):**
```text
1|1|2|1234|form_0001.pdf|M|||||||||||INFO01|POST0001|출력
2|3|5||form_0002.pdf|M|||||||||||INFO01|POST0002|출력
```

* **주요 항목 설명:**
  * `시작페이지` / `종료페이지`: 원본 PDF에서 분할할 페이지 범위(정수)입니다.
  * `패스워드`: 개별 PDF에 AES-256 사용자 비밀번호를 적용할 경우 입력합니다(공란이면 암호화하지 않음).
  * `파일명`: 분할 후 저장될 파일명입니다. 결과 이동 시 `form_*.pdf` 패턴의 파일을 대상으로 복사합니다.
  * `infoid`: 결과 파일이 복사될 최종 하위 폴더명(`{출력경로}\{M 또는 E}\{yyyyMMdd}\{infoid}\`)으로 사용됩니다.
  * `postid`: 짝수 번째 레코드 처리 시 시작/종료 페이지 좌측 하단 영역(5mm, 0mm, 폭 30mm, 높이 5mm)의 텍스트와 일치하는지 무결성을 검증하는 값입니다.
  * `출력여부`: 값이 `출력`인 레코드만 분할 대상으로 처리됩니다.

### 3. 처리 결과
분할이 완료된 `form_*.pdf` 파일은 설정된 출력 경로 하위의 `{M 또는 E}\{yyyyMMdd}\{infoid}\` 폴더로 복사되며, 복사 완료 후 작업 폴더의 임시 원본 파일은 정리됩니다.
