테스트에 대한 과정과 결과를 기록
===========================

# 테스트는 과정 기록을 위해서 master 브랜치가 아닌 test 브랜치에서 수행함.

## 1번 테스트

#### 테스트 시행 전 상황 

* ###### 브랜치 목록 

1. master(local in desktop)
2. test-amend(local in desktop)
3. origin/master(remote)
4. origin/test-amend(remote)

* ###### 브랜치별 커밋 목록 

1. master 

bd8e25414bd55531bb0dc6864c142df944ab1e00 (HEAD -> master, origin/master) README.md add 주의
77ede9464455dfe7646161e925d87d0f3618eb46 README.md add accent ordered list for situations and its expectations
92a323f422b31f3b50d59780eeeb4c55fe0f838f README.md add markdowns
8b3c44b1fb91607c9bdc41b0512700a526fb9b1c README.md first commit

2. test-amend

423b89f29cc2b9f4720d8c067ad4f558f0795239 (HEAD -> test-amend, origin/test-amend) 앗 실수
bd8e25414bd55531bb0dc6864c142df944ab1e00 (origin/master, master) README.md add 주의
77ede9464455dfe7646161e925d87d0f3618eb46 README.md add accent ordered list for situations and its expectations
92a323f422b31f3b50d59780eeeb4c55fe0f838f README.md add markdowns
8b3c44b1fb91607c9bdc41b0512700a526fb9b1c README.md first commit
e4bd2b3d1e42fb084e47ccb50c0020cd6538add3 test.html h1 tag and content added
f06d4bfac4ac8327908b80dc3581d582ab82355d test.html title changed
36a96efacf41a34320b87bddecf9d6f71d6ad530 test.html first commit

3. origin/master

master와 동일

4. origin/test-amend

test-amend와 동일