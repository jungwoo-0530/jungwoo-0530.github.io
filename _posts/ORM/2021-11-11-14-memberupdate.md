---
title:  "엔티티 업데이트"
excerpt: ""

categories:
  - Jpa
tags:
  - [Jpa, Web, Update]
 
date: 2021-11-14
last_modified_at: 2021-11-14
---



## 엔티티 업데이트



내가 한 토이프로젝트를 예로 업데이트에 대해서 작성을 하려고 한다.

```java
@GetMapping("/admin/users/edit/{memberId}")
    public String updateUserByAdmin(@PathVariable("memberId") Long memberId,
                                    Model model) {

        Member findOne = memberService.findOneById(memberId);

        MemberDto result = new MemberDto(memberId, findOne.getName(), findOne.getNickname(),
                findOne.getEmail(), findOne.getRole());

        model.addAttribute("memberDto", result);
        model.addAttribute("roleTypes", RoleTypes.values());

        return "users/admin/updateUserByAdmin";
    }


    @PostMapping("/admin/users/edit/{memberId}")
    public String updateUserByAdmin(@PathVariable("memberId") Long memberId,
                                    @ModelAttribute("memberDto") MemberDto memberDto) {

        memberService.updateByAdmin(memberId, memberDto.getNickname(),memberDto.getRole());

        return "redirect:/admin/users";
    }
```



업데이트 방법에는 아래와 같다.

1. dirty check

2. merge



merge로 업데이트 하는 방법은 준영속 상태에서의 id값으로 db에서 조회해서 영속 컨텍스트에 가져온 후, 준영속 엔티티 모든 값들로 영속 컨텍스트에 있는 엔티티 값들을 변경한다.

-> 문제는 모든 값을 바꾸므로 null인 값이 들어간다.

-> 특별한 조건아닌 경우에는 사용을 하면 안된다.

-> 참고로 준영속 상태의 엔티티는 jpa가 더 이상 관리하지 않는 엔티티.

dirty check는 영속성 컨텍스트에 있는 엔티티를 변경된 값들만 커밋 시점에 변경 해주는 것.

-> 즉, 영속성 컨텍스트에 있어야 하므로 조회를 해야한다.

-> 변경하는 로직을 짜야한다.



MemberService.java

```java
@Transactional
    public void updateByAdmin(Long id,String nickname, String role) {
        Member member = memberRepository.findOneById(id);

        member.change(nickname, role);
    }
```

```java
@Entity
@Getter
@Setter
@NoArgsConstructor
public class Member {

  ...

    public Member(String email, String password, String nickname, String name, String role) {
	...
    }

    public void change(String nickname, String role) {
        this.setNickname(nickname);
        this.setRole(role);
    }

}
```

커밋시점에 더티 체킹이 일어나므로 트랜잭션이 필수.

`MemberService`에서 `findOneById()`로 영속성 컨텍스트에 조회한 엔티티를 넣고

`change()` 라는 비지니스 로직으로 더티체킹을 한다.

커밋 시점에 jpa가 자동으로 바뀐 값을 확인하고 update 쿼리를 날린다.

