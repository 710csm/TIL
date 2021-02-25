# TabBar

## 웹뷰 호출시 Tabbar가 계속 남아 있을 때       
1. viewWillDisappear 안에서 숨긴다
'''
override func viewWillDisappear(_ animated: Bool) {
    super.viewWillDisappear(animated)
    self.tabBarController?.tabBar.isHidden = true
}
'''

2. viewDidAppear 안에서 다시 보이게 설정
'''
override func viewDidAppear(_ animated: Bool) {
    super.viewDidAppear(animated)
    self.tabBarController?.tabBar.isHidden = false
}
'''
