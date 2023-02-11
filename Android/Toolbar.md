# Android

## Navigation 요소와 toolbar 연결

#### **적용방법**
1. 기존 액션바를 `setSupportActionBar()` 메소드를 통해 Toolbar 로 대체
2. navController 연결
``` 
val navHostFragment =
        supportFragmentManager.findFragmentById(R.id.nav_host_fragment) as NavHostFragment
val navController = navHostFragment.navController
```
3. toolbar의 title을 현재 프래그먼트로 변경
``` 
navController.addOnDestinationChangedListener {
                controller, destination, arguments ->
            binding.toolbarTitle.text = navController.currentDestination?.label.toString()
        }
``` 
4. toolbar와 navigation 연결
``` 
appBarConfiguration = AppBarConfiguration(navController.graph)
setupActionBarWithNavController(navController, appBarConfiguration)
``` 
(Reference) https://developer.android.com/guide/navigation/navigation-ui?hl=ko

---

## Activity의 toolbar를 Fragment 마다 menu 변경하는 방법
! 기존 `setHasOptionsMenu(true)` 함수 deprecated

#### **적용방법**
1. Toolbar를 보여줄 Fragment 에서 `requireActivity()` 메소드로 현재 Fragment를 가져옴
2. 메뉴 아이템을 보여주기 위해 MenuProvicer 인터페이스를 구현
2. menuInflater.inflate 메소드를 통해 적용할 menu xml 추가
3. onMenuItemSelected 메소드 안에 inflate 한 xml 안의 메뉴 버튼 클릭했을 때 이벤트 처리
```
val menuHost: MenuHost = requireActivity()
    menuHost.addMenuProvider(object : MenuProvider {
        override fun onCreateMenu(menu: Menu, menuInflater: MenuInflater) {
            // Add menu items here
            menuInflater.inflate(R.menu.menu_category_detail, menu)
        }

        override fun onMenuItemSelected(menuItem: MenuItem): Boolean {
            return when (menuItem.itemId) {
                R.id.menu_confrim_button -> {
                    Toast.makeText(context,"수정 되었습니다",Toast.LENGTH_SHORT).show()
                    true
                }
                else -> false
            }
        }
    }, viewLifecycleOwner, Lifecycle.State.RESUMED)
```