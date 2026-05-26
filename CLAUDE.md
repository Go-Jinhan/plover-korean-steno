# Plover 설정 가이드

## 현재 환경

- **시스템**: Korean Modern C (한국어 CAS 자판)
- **머신**: Keyboard
- **플러그인**: `plover_korean` v0.0.3, `plover_python_dictionary` v1.2.1

---

## 딕셔너리 구성

| 파일 | 역할 |
|------|------|
| `ko_cas_numbers.py` | Korean CAS 숫자 딕셔너리 (플러그인 내장) |
| `ko_cas_base.py` | Korean CAS 기본 딕셔너리 (플러그인 내장) |
| `user.json` | 사용자 커스텀 딕셔너리 (비어 있음, 개인 추가용) |
| `main.json` | 기본 영어 딕셔너리 (영어 시스템 전용) |
| `commands.json` | Plover 커맨드 딕셔너리 (영어 시스템 전용) |

---

## 중요: Korean 시스템에서 user.json 유지 문제

### 증상
Plover 재시작 후 Korean Modern C 시스템의 딕셔너리 목록에서 `user.json`이 사라진다.

### 원인
`plover_korean` 플러그인이 재시작 시 `DEFAULT_DICTIONARIES`로 딕셔너리 목록을 리셋한다.
`plover.cfg`에 추가해도 무시됨.

### 적용한 해결책
플러그인 파일을 직접 수정하여 `user.json`을 기본 딕셔너리 목록에 추가했다.

**수정 파일**:
```
~/Library/Application Support/plover/plugins/mac/lib/python/site-packages/plover_korean/system/cas/definition.py
```

**변경 내용** (파일 맨 아래 `DEFAULT_DICTIONARIES`):
```python
# 변경 전
DEFAULT_DICTIONARIES: List[str] = [
    os.path.join(_DICT_DIR, 'ko_cas_numbers.py'),
    os.path.join(_DICT_DIR, 'ko_cas_base.py'),
]

# 변경 후
DEFAULT_DICTIONARIES: List[str] = [
    os.path.join(_DICT_DIR, 'ko_cas_numbers.py'),
    os.path.join(_DICT_DIR, 'ko_cas_base.py'),
    os.path.expanduser('~/Library/Application Support/plover/user.json'),
]
```

### 주의
`plover_korean` 플러그인 업데이트 시 이 변경이 덮어써질 수 있다.
업데이트 후 `user.json`이 다시 사라지면 위 수정을 다시 적용할 것.

---

## Output 토글 방법

Plover Configuration > Output 탭에는 단축키 설정이 없다.
`user.json`에 스테노 스트로크로 토글을 등록해야 한다.

### 현재 설정된 토글 스트로크

| 스트로크 | 실제 키 (QWERTY) | 동작 |
|---------|----------------|------|
| `1ㅎ` | `1` + `q` 동시 | Output Enabled ↔ Disabled 토글 |
| `2ㅎ` | `2` + `q` 동시 | Output 강제 활성화 (Resume) |
| `3ㅎ` | `3` + `q` 동시 | Output 강제 비활성화 (Suspend) |

### 스트로크 형식 규칙 (Korean CAS)

Korean CAS 스트로크 형식: `[숫자1-5][초성][모음][숫자6-0][종성]`

**숫자 + 자음 조합**은 `ko_cas_base.py`(숫자 있으면 KeyError)와 `ko_cas_numbers.py`(자음 있으면 KeyError) 모두 처리하지 않으므로, `user.json`에서만 처리되는 안전한 커맨드 스트로크로 사용 가능하다.

---

## plover.cfg 현재 상태

```ini
[Machine Configuration]
machine_type = Keyboard

[Output Configuration]
undo_levels = 100

[System]
name = Korean Modern C

[System: Korean Modern C]
dictionaries = [
  {"enabled": true, "path": "plugins/mac/lib/python/site-packages/plover_korean/system/cas/dictionaries/ko_cas_numbers.py"},
  {"enabled": true, "path": "plugins/mac/lib/python/site-packages/plover_korean/system/cas/dictionaries/ko_cas_base.py"},
  {"enabled": true, "path": "user.json"}
]

[Appearance]
mode = system
```
