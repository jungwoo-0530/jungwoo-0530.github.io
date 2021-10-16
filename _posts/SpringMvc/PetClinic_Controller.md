# PetClinic Controller 분석



## OwnerController



#### setAllowedFields

```java
@InitBinder
	public void setAllowedFields(WebDataBinder dataBinder) {
		dataBinder.setDisallowedFields("id");
	}
```

- InitBinder 애노테이션을 사용하여 OwnerController에 있는 핸들러(메소드)들을 바인딩 또는 검증 설정을 변경하고 싶을 때 사용

  - 해당 메소드는 id 필드값을 disable. 모든 핸들러는 id 필드값이 NULL로 찍힘.

  

#### initCreationForm

```java
	@GetMapping("/owners/new")
	public String initCreationForm(Map<String, Object> model) {
		Owner owner = new Owner();
		model.put("owner", owner);
		return VIEWS_OWNER_CREATE_OR_UPDATE_FORM;
	}
```

- @GetMapping("/owners/new") : HTTP GET 요청, /owners/new 요청이 들어오면 이 핸들러로 맵핑.

- Map으로 String과 Object를 받는다. Map을 사용했으므로 put.

  - put(attributeName, attributeValue);
  - Name을 owner를 사용했으므로 view(HTML)에서 object = "${owner}"를 사용.

- 리턴으로 VIEWS_OWNER_CREATE_OR_UPDATE_FORM을 하게 되는데 이 것은 이 컨트롤러에서 

  - ```java
    private static final String VIEWS_OWNER_CREATE_OR_UPDATE_FORM = "owners/createOrUpdateOwnerForm";
    ```

    로 설정한 것. 즉 owners/createOrUpdateOwnerForm view를 보여준다.

  

- 즉, 해당 메소드의 역할은 새로운 오너를 등록하기 위해서 Owner객체를 초기화해주고 view를 보여주는 핸들러.



#### processCreationForm

```java
	@PostMapping("/owners/new")
	public String processCreationForm(@Valid Owner owner, BindingResult result) {
		if (result.hasErrors()) {
			return VIEWS_OWNER_CREATE_OR_UPDATE_FORM;
		}
		else {
			this.owners.save(owner);
			return "redirect:/owners/" + owner.getId();
		}
	}
```

- @PostMapping("/owners/new") : HTTP POST(등록)로 /owners/new로 요청이 들어오면 이 핸들러로 맵핑

- @Valid를 사용하여 Owner 클래스에서 정의한 오너의 필드값을 검증한다.

  - ```java
      @Column(name = "address")
    	@NotEmpty
    	private String address;
    
    	@Column(name = "city")
    	@NotEmpty
    	private String city;
    
    	@Column(name = "telephone")
    	@NotEmpty
    	@Digits(fraction = 0, integer = 10)
    	private String telephone;
    ```

    

- BindingResult를 사용하여 바인딩에러를 처리한다. result.hasErrors()가 true일 경우 다시 view를 보여줘서 재입력하도록 유도.

- 검증 결과 이상이 없다면 repository에 저장하고 리턴으로 /owner/id로 맵핑해준다.
  
  - 즉, showOwner로 맵핑됨.

#### initFindForm

```java
	@GetMapping("/owners/find")
	public String initFindForm(Map<String, Object> model) {
		model.put("owner", new Owner());
		return "owners/findOwners";
	}

```

- @GetMapping("/owners/find") : HTTP GET 요청, /owners/find 요청이 들어오면 이 핸들러로 맵핑.
- Owner 새로운 객체를 owner이름으로 만든 후, owners/findOwners 이름의 view를 리턴
- owners/findOwners view에서 폼서브밋이 존재한다. 폼 서브밋은 <form>태그에 존재하는 데이터를 서버에 전달해준다. action = "@{/owners}"가 있으며 /owners 페이지로 이동.
  - 그렇다면 @GetMapping("/owners")으로 인해 processFindForm 핸들러로 맵핑된다.

#### processFindForm

```java
	@GetMapping("/owners")
	public String processFindForm(@RequestParam(defaultValue = "1") int page, Owner owner, BindingResult result,
			Model model) {

		// allow parameterless GET request for /owners to return all records
		if (owner.getLastName() == null) {
			owner.setLastName(""); // empty string signifies broadest possible search
		}

		// find owners by last name
		String lastName = owner.getLastName();
		Page<Owner> ownersResults = findPaginatedForOwnersLastName(page, lastName);
		if (ownersResults.isEmpty()) {
			// no owners found
			result.rejectValue("lastName", "notFound", "not found");
			return "owners/findOwners";
		}
		else if (ownersResults.getTotalElements() == 1) {
			// 1 owner found
			owner = ownersResults.iterator().next();
			return "redirect:/owners/" + owner.getId();
		}
		else {
			// multiple owners found
			lastName = owner.getLastName();
			return addPaginationModel(page, model, lastName, ownersResults);
		}
	}
```

- @GetMapping("/owners") : HTTP GET 요청, /owners 요청이 들어오면 이 핸들러로 맵핑.
- @RequestParam(defaultValue = "1") int page : GET 방식으로 넘어온 URI의 쿼리스트링을 받아 올 때 사용. 
  - name을 따로 설정 안했기에 page라는 key값을 쿼리스트링을 받아오며 그 디폴트 value로 1을 설정한다.
  - <img src="img/image-20211016064117965.png" alt="image-20211016064117965" style="width:50%;" />
- 기본적인 등록된 오너 찾는 핸들러
- 검색창에서 아무런 데이터 없이 find한다면 LastName을 ""
- Page는 JPA와 관련된 내용. 공부 후 다시 분석.
- 검색 결과가 없다면 다시 입력받을 수 있게 owners/findOwners view를 보여줌
- 검색 결과가 하나라면 해당하는 /owners/id를 맵핑되는 핸들러로 리다이렉트.
- 검색 결과가 여러개라면 addPaginationModel 메소드로 파라미터를 넘겨준다.



#### addPaginationModel

```java
	private String addPaginationModel(int page, Model model, String lastName, Page<Owner> paginated) {
		model.addAttribute("listOwners", paginated);
		List<Owner> listOwners = paginated.getContent();
		model.addAttribute("currentPage", page);
		model.addAttribute("totalPages", paginated.getTotalPages());
		model.addAttribute("totalItems", paginated.getTotalElements());
		model.addAttribute("listOwners", listOwners);
		return "owners/ownersList";
	}

```

- processFindForm핸들러에서 Owner의 검색결과가 여러개라면 넘어오는 메소드
- 모델에 정보를 넣어주고 owners/ownersList view를 보여줌.



#### findPaginatedForOwnersLastName

```java
	private Page<Owner> findPaginatedForOwnersLastName(int page, String lastname) {

		int pageSize = 5;
		Pageable pageable = PageRequest.of(page - 1, pageSize);
		return owners.findByLastName(lastname, pageable);

	}
```



#### initUpdateOwnerForm

```java
	@GetMapping("/owners/{ownerId}/edit")
	public String initUpdateOwnerForm(@PathVariable("ownerId") int ownerId, Model model) {
		Owner owner = this.owners.findById(ownerId);
		model.addAttribute(owner);
		return VIEWS_OWNER_CREATE_OR_UPDATE_FORM;
	}
```

- @GetMapping("/owners/{ownerId}/edit") : HTTP GET 요청으로 ownerId가 들어온다.
- @PathVariable("ownerId") int ownerId : PathVariable을 사용하여 URI에서 ownerId와 일치하는 정보를 가져온다.
- ownerId를 사용하여 일치하는 오너를 찾은후에 owner에 넣고 모델에 정보를 넣는다.
- 리턴을 VIEWS_OWNER_CREATE_OR_UPDATE_FORM라는 view를 보여주며 owner를 같이 넘겨준다.



#### processUpdateOwnerForm

```java
	@PostMapping("/owners/{ownerId}/edit")
	public String processUpdateOwnerForm(@Valid Owner owner, BindingResult result,
			@PathVariable("ownerId") int ownerId) {
		if (result.hasErrors()) {
			return VIEWS_OWNER_CREATE_OR_UPDATE_FORM;
		}
		else {
			owner.setId(ownerId);
			this.owners.save(owner);
			return "redirect:/owners/{ownerId}";
		}
	}
```

- 오너의 정보를 에딧하고 등록을 한다.
- 입력한 값들이 바인딩 에러가 나올시 다시 입력받을 수 있게 뷰를 보여준다
- 입력한 값들이 정상이라면 그 정보를 저장하고 /owners/{ownerId} 요청을 맵핑하는 핸들러로 리다이렉트된다.

#### showOwner

```java
	@GetMapping("/owners/{ownerId}")
	public ModelAndView showOwner(@PathVariable("ownerId") int ownerId) {
		ModelAndView mav = new ModelAndView("owners/ownerDetails");
		Owner owner = this.owners.findById(ownerId);
		for (Pet pet : owner.getPets()) {
			pet.setVisitsInternal(visits.findByPetId(pet.getId()));
		}
		mav.addObject(owner);
		return mav;
	}
```

- @GetMapping("/owners/{ownerId}") : HTTP GET으로 /owners/{ownerId} 요청이 들어오면 이 핸들러로 맵핑한다.
- ModelAndView는 모델과 뷰를 동시에 설정 가능하다. 
  - ModelAndView mav = new ModelAndView("owners/ownerDetails");에서 owners/ownerDetails로 view를 설정
  - mav.addObject(owner);에서 뷰로 보낼 데이터 값을 넣는다.
  - 리턴은 모델앤뷰를 리턴하여 뷰를 보여주며 addObject했던 값도 같이 넘겨준다.



## PetController



#### populatePetTypes

```java

```



#### findOwner

```java

```



#### initOwnerBinder

```java

```



#### initPetBinder

```java

```



#### initCreationForm

```java

```



#### processCreationForm

```java

```



#### initUpdateForm

```java

```



#### processUpdateForm

```java

```



## VisitController



#### SetAllowedFields

```java

```



#### loadPetWithVisit

```java

```



#### initNewVisitForm

```java

```



#### processNewVisitForm

```java

```



## CrashController



#### triggerException

```java

```



## VetController



#### showVetList

```java

```



#### addPaginationModel

```java

```



#### findPaginated

```java

```



#### showResourcesVetList

```java

```

