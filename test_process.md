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

commit 1f9fd3dbc212573c30874cbc788bf1df7f05403b (HEAD -> master, origin/master)
bd8e25414bd55531bb0dc6864c142df944ab1e00 README.md add 주의
77ede9464455dfe7646161e925d87d0f3618eb46 README.md add accent ordered list for situations and its expectations
92a323f422b31f3b50d59780eeeb4c55fe0f838f README.md add markdowns
8b3c44b1fb91607c9bdc41b0512700a526fb9b1c README.md first commit
생략...

2. test-amend

423b89f29cc2b9f4720d8c067ad4f558f0795239 (HEAD -> test-amend, origin/test-amend) 앗 실수
bd8e25414bd55531bb0dc6864c142df944ab1e00 (origin/master, master) README.md add 주의
77ede9464455dfe7646161e925d87d0f3618eb46 README.md add accent ordered list for situations and its expectations
92a323f422b31f3b50d59780eeeb4c55fe0f838f README.md add markdowns
8b3c44b1fb91607c9bdc41b0512700a526fb9b1c README.md first commit
생략...

3. origin/master

master와 동일

4. origin/test-amend

test-amend와 동일

#### 테스트 과정 

1. git checkout test-amend

2. git commit -amend 

3. git push origin test-amend 

#### 테스트 결과 

###### test-amend 커밋 목록 

1f9fd3dbc212573c30874cbc788bf1df7f05403b (HEAD -> master, origin/master) test_process.md first commit
bd8e25414bd55531bb0dc6864c142df944ab1e00 README.md add 주의
77ede9464455dfe7646161e925d87d0f3618eb46 README.md add accent ordered list for situations and its expectations
92a323f422b31f3b50d59780eeeb4c55fe0f838f README.md add markdowns
8b3c44b1fb91607c9bdc41b0512700a526fb9b1c README.md first commit

기존의 __423b89f2__ 커밋이 __1f9fd3db__ 커밋으로 덮어쓰여짐. 

git push origin test-amend를 진행했을 때 에러가 발생. 

에러 내용 

 ! [rejected]        test-amend -> test-amend (non-fast-forward)
error: failed to push some refs to 'github.com:ShangBinLee/git-commit-amend-test.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.

__hint__ 에서는 git pull을 해보라고 하지만 git pull을 해도 tracking information이 없다(병합할게 없다.)는 메시지만 뜬다. 

즉, 실제로 현재 브랜치가 뒤쳐졌다는 얘기는 pull을 안 해서가 아니라 HEAD 커밋의 해시값이 local과 remote에서 각각 다르기 때문에 발생하는 문제인 것으로 예측된다. 