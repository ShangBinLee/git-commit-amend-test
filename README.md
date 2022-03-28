repository에 push한 상태에서 5가지 경우로 나누어서 테스트
===================================================
### 참여하는 contributor
##### 1. desktop
##### 2. notebook

__밑의 상황은 desktop과 notebook에서 remote repository를 최신으로 업데이트 갱신해놓은 상태에서 진행함.__
* * *

## 5가지 상황 

1. __desktop에서 push하고, notebook에서 pull하지 않은 상태에서 desktop에서 --amend로 커밋 메시지 수정 후에 git push 해보고 안 되면 git push --force-with-lease__

2. __desktop에서 push한 것을, notebook에서 pull한 상태에서 notebook에서 작업을 하지 않고 desktop에서만 --amend로 커밋 메시지 수정 후에 git push, git push --force-with-lease__

3. __2번에서 notebook에서 작업을 진행한 후, desktop에서만 --amend로 커밋 메시지 수정 후에 git push, git push --force-with-lease__

4. __3번에서 notebook에서 push하고 desktop에서 pull하지 않고 --amend로 커밋 메시지 수정 후에 git push, git push --force-with-lease__

5. __4번에서 desktop에서 pull하고 --amend로 커밋 메시지 수정 후에 git push, git push --force-with-lease__

1번 후에 2번에서 notebook에서 pull하는 것으로 정상적으로 되는지 확인할 수 있을 듯.

## 예상

1. __notebook에서 pull하지 않았는데 push 혹은 push -f를 하는 것이므로 정상적으로 pull도 되고 이후에 작업을 하더라도 연동된 것으로 하기 때문에 삭제된 커밋이 되살아나거나 하는 일은 없을듯.__

2. __작업은 하지 않았으므로 1번처럼 될듯.__

3. __3번이 끝난 뒤에 notebook에서 pull한다고 할 때, 병합 과정에서 충돌이 없다고 가정할 때는 정상적으로 병합되고 notebook 측의 working directory와 stage도 문제 없이 유지될 것으로 보임.__  

4. __문제가 발생할 것 같은 부분. notebook에서 push한 내용을 desktop에서 덮어쓰기 때문에 기존의 push된 commit이 유실될 뿐만 아니라 pull하더라도 문제가 생길 수 있을 것 같음.(notebook의 head가 remote repository의 head보다 앞서있는 A/HEAD 상태일 것.)__

5. __기본적으로 desktop에서 pull하면 notebook에서 pull하지 않았기 때문에 서로 최신 상태라 push를 해도 문제가 없을 것으로 보인다.__


고로 결론은, 서로 최신 상태가 아닌데 한 쪽이 git push --force를 하게 되면 문제가 생길 것으로 예상한다. 