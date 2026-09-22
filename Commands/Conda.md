# Conda 명령어

**환경 관리**
```bash
conda info --envs               # 환경 목록 확인
conda create -n <이름> python=3.11   # 환경 생성
conda activate <이름>           # 환경 활성화
conda deactivate                # 환경 비활성화
conda remove -n <이름> --all    # 환경 삭제
conda rename -n <이름> <새이름> # 환경 이름 변경
```

**패키지 관리**
```bash
conda install <패키지>          # 패키지 설치
conda install <패키지>=1.2.3    # 버전 지정 설치
conda install -c conda-forge <패키지>  # conda-forge 채널에서 설치
conda update <패키지>           # 패키지 업데이트
conda remove <패키지>           # 패키지 제거
conda list                      # 설치된 패키지 목록
```

**환경 내보내기 / 복원**
```bash
conda env export > environment.yml            # 환경 내보내기
conda env export --no-builds > environment.yml  # OS 의존성 없이 내보내기
conda env create -f environment.yml           # 환경 복원
conda env update -f environment.yml           # 환경 업데이트
```

**채널**
```bash
conda config --add channels conda-forge    # 채널 추가
conda config --show channels               # 채널 목록 확인
```

**기타**
```bash
conda clean --all               # 캐시 및 불필요한 파일 정리
conda update conda              # conda 자체 업데이트
conda search <패키지>           # 패키지 검색
```
