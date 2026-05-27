# plover-korean-steno

macOS에서 [Plover](https://www.openstenoproject.org/) 스테노 소프트웨어를 Korean Modern C (한국어 CAS) 자판으로 사용하기 위한 설정 파일 모음입니다.

## 환경

| 항목 | 내용 |
|------|------|
| OS | macOS |
| 입력 장치 | Keyboard (일반 키보드) |
| 스테노 시스템 | Korean Modern C (한국어 CAS) |
| 주요 플러그인 | plover_korean v0.0.3, plover_python_dictionary v1.2.1 |

## 파일 구성

| 파일/폴더 | 설명 |
|-----------|------|
| `user.json` | 사용자 커스텀 딕셔너리 (Output 토글 스트로크 등) |
| `plover.cfg` | Plover 전체 설정 |
| `ko_cas_base.py` | Korean CAS 기본 한글 조합 딕셔너리 |
| `ko_cas_numbers.py` | Korean CAS 숫자 딕셔너리 |
| `ko_cas_briefs.json` | Korean CAS 한글 기본 약어 딕셔너리 (기본 문장부호, 대명사, 접속사 등) |
| `ko_cas_particles.json` | Korean CAS 조사 딕셔너리 |
| `ko_cas_conjunctions.json` | Korean CAS 접속사 딕셔너리 |
| `ko_cas_conjugations.json` | Korean CAS 활용/어미 딕셔너리 |
| `ko_cas_block_briefs.json` | Korean CAS 음절 약어 딕셔너리 |
| `ko_cas_numbers_and_units.json` | Korean CAS 숫자 및 단위 딕셔너리 |
| `ko_cas_commands.json` | Korean CAS 기능/명령어 딕셔너리 |
| `ko_cas_symbols.json` | Korean CAS 기호 딕셔너리 |
| `ko_cas_single_keys.json` | Korean CAS 단일 키 딕셔너리 |
| `ko_cas_english_fingerspelling.json` | Korean CAS 영어 핑거스펠링 딕셔너리 |
| `plugins/` | 설치된 플러그인 (`definition.py` 패치 포함) |

## 설치 방법

### 1. Plover 설치

[Plover 공식 사이트](https://www.openstenoproject.org/)에서 macOS용 Plover를 다운로드하여 설치합니다.

### 2. 플러그인 설치

Plover를 실행한 뒤 Plugin Manager에서 다음 플러그인을 설치합니다.

- `plover_korean`
- `plover_python_dictionary`

### 3. 설정 파일 복사

Plover를 종료한 뒤, 이 레포를 클론하여 `~/Library/Application Support/plover/`에 파일을 복사합니다.

```bash
git clone https://github.com/Go-Jinhan/plover-korean-steno.git
cd plover-korean-steno
cp -r . ~/Library/Application\ Support/plover/
```

> `plugins/` 폴더에는 아래에서 설명하는 `definition.py` 패치가 이미 적용되어 있습니다.

## ⚠️ Korean plugin 패치 — user.json 유지 문제

### 증상

Plover 재시작 후 Korean Modern C 시스템의 딕셔너리 목록에서 `user.json`이 사라집니다.

### 원인

`plover_korean` 플러그인이 재시작 시 내부의 `DEFAULT_DICTIONARIES` 값으로 딕셔너리 목록을 덮어씁니다. `plover.cfg`에 수동으로 추가해도 재시작 시 무시됩니다.

### 해결 방법

플러그인의 `definition.py`를 직접 수정하여 `user.json`을 기본 딕셔너리에 추가합니다.

**수정 파일 경로:**
```
~/Library/Application Support/plover/plugins/mac/lib/python/site-packages/plover_korean/system/cas/definition.py
```

**변경 내용:**
```python
# 변경 전
DEFAULT_DICTIONARIES: List[str] = [
    os.path.join(_DICT_DIR, 'ko_cas_numbers.py'),
    os.path.join(_DICT_DIR, 'ko_cas_base.py'),
]

# 변경 후
DEFAULT_DICTIONARIES: List[str] = [
    os.path.join(_DICT_DIR, 'ko_cas_numbers.py'),
    os.path.join(_DICT_DIR, 'ko_cas_briefs.json'),
    os.path.join(_DICT_DIR, 'ko_cas_symbols.json'),
    os.path.join(_DICT_DIR, 'ko_cas_commands.json'),
    os.path.join(_DICT_DIR, 'ko_cas_english_fingerspelling.json'),
    os.path.join(_DICT_DIR, 'ko_cas_single_keys.json'),
    os.path.join(_DICT_DIR, 'ko_cas_block_briefs.json'),
    os.path.join(_DICT_DIR, 'ko_cas_conjugations.json'),
    os.path.join(_DICT_DIR, 'ko_cas_conjunctions.json'),
    os.path.join(_DICT_DIR, 'ko_cas_numbers_and_units.json'),
    os.path.join(_DICT_DIR, 'ko_cas_particles.json'),
    os.path.join(_DICT_DIR, 'ko_cas_base.py'),
    os.path.expanduser('~/Library/Application Support/plover/user.json'),
]
```

## Output 토글 스트로크

Plover Configuration UI에는 Output 단축키 설정이 없습니다. `user.json`에 스테노 스트로크로 등록하여 사용합니다.

Korean CAS 스트로크 형식: `[숫자 1–5][초성][모음][숫자 6–0][종성]`

숫자 + 자음 조합은 `ko_cas_base.py`와 `ko_cas_numbers.py` 모두 처리하지 않으므로, `user.json`에서만 처리되는 안전한 커맨드 스트로크로 활용할 수 있습니다.

| 스트로크 | 실제 키 (QWERTY) | 동작 |
|----------|-----------------|------|
| `1ㅎ` | `1` + `q` 동시 입력 | Output 토글 (ON ↔ OFF) |
| `2ㅎ` | `2` + `q` 동시 입력 | Output 강제 활성화 (Resume) |
| `3ㅎ` | `3` + `q` 동시 입력 | Output 강제 비활성화 (Suspend) |

## 플러그인 업데이트 시 주의사항

`plover_korean` 플러그인을 업데이트하면 `plugins/` 폴더가 교체되어 `definition.py` 패치가 초기화될 수 있습니다.

업데이트 후 확인 절차:

1. Plover 재시작
2. Korean Modern C 시스템의 딕셔너리 목록에 `user.json` 포함 여부 확인
3. 없으면 위의 패치를 다시 적용
4. 이 레포의 `plugins/` 폴더를 커밋하여 최신 상태 유지

## 참고

- [Open Steno Project](https://www.openstenoproject.org/)
- [plover_korean GitHub](https://github.com/nsmarkop/plover_korean)
- [Plover Wiki](https://plover.wiki/)
