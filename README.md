# pythonStudy24
파이썬 ai 기초 학습용

MBC 아카데미 컴퓨터 교육센터 수원점에서 AI기초 학습으로 파이썬 학습 진행용

https://wikidocs.net/book/1

https://colab.research.google.com/drive/1x64OxiLTS91TfX3jqfW8U8f2MP06hzb2?usp=sharing


```
# 미션
# 관리자가 커피 가격과 커피명을 정하고 개수를 입력한다.
# 소비자가 커피를 구매하는데 잔돈이 나와야 함
# 판매 종료 후 관리자가 커피 판매한 총액을 파악할 수 있어야함

coffeeList = [
  {'name': '에스프레소', 'price': 3500, 'stock': 0},
  {'name': '아메리카노', 'price': 4000, 'stock': 0},
  {'name': '카푸치노', 'price': 5000, 'stock': 2}
]

totalSalesValue = 0  # 매출총액


# 돈계산 함수
def calcMoney(userMoney, coffee, quantity):
  global totalSalesValue

  totalPrice = coffee['price'] * quantity  # 총 금액 계산

  if isinstance(userMoney, int):  # int 이외 입력 시 예외처리 필요
    if userMoney >= totalPrice:
      coffee['stock'] -= quantity  # 구매한 수량만큼 재고 차감
      totalSalesValue += totalPrice
      print(f"드세요. 잔돈 : {userMoney - totalPrice}원")
      return userMoney - totalPrice  # 거스름돈 반환
    else:
      print("돈이 부족합니다.")
    return userMoney


# 관리자 모드 함수 (추가/수정/삭제 메뉴 적용)
def adminMode():
  while True:
    print("""
    == 관리자 모드 ==
    [1] 커피 추가
    [2] 커피 수정
    [3] 커피 삭제
    [4] 종료
    """)
    try:
      choice = int(input("작업을 선택하세요: "))
      if choice == 1:
        addCoffee()
      elif choice == 2:
        modifyCoffee()
      elif choice == 3:
        deleteCoffee()
      elif choice == 4:
        print("관리자 모드를 종료합니다.\n")
        break
      else:
        print("유효한 번호를 입력하세요.")
    except ValueError:
      print("숫자를 입력해주세요.")


# 커피 추가 함수
def addCoffee():
  print("\n== 커피 추가 ==")
  name = input("추가할 커피 이름을 입력하세요: ")
  try:
    price = int(input("커피 가격을 입력하세요: "))
    stock = int(input("커피 재고를 입력하세요: "))
    coffeeList.append({'name': name, 'price': price, 'stock': stock})
    print(f"{name}가 메뉴에 추가되었습니다.\n")
  except ValueError:
    print("가격과 재고는 숫자로 입력해주세요.")


# 커피 수정 함수
def modifyCoffee():
  print("\n== 커피 수정 ==")
  print("수정할 커피를 선택하세요:")
  showCoffeeList()
  try:
    coffeeIndex = int(input("번호를 입력하세요: ")) - 1
    if 0 <= coffeeIndex < len(coffeeList):
      coffee = coffeeList[coffeeIndex]
      print(f"현재 커피: {coffee}")
      newName = input(f"새 이름 (현재: {coffee['name']}): ")
      newPrice = input(f"새 가격 (현재: {coffee['price']}) (숫자 입력): ")
      newStock = input(f"새 재고 (현재: {coffee['stock']}) (숫자 입력): ")

      # 입력된 값이 있을 경우만 수정
      if newName:
        coffee['name'] = newName
      if newPrice:
        coffee['price'] = int(newPrice)
      if newStock:
        coffee['stock'] = int(newStock)

      print("커피 정보가 수정되었습니다.\n")
    else:
      print("올바른 번호를 입력해주세요.")
  except ValueError:
    print("유효한 숫자를 입력해주세요.")


# 커피 삭제 함수
def deleteCoffee():
  print("\n== 커피 삭제 ==")
  print("삭제할 커피를 선택하세요:")
  showCoffeeList()
  try:
    coffeeIndex = int(input("번호를 입력하세요: ")) - 1
    if 0 <= coffeeIndex < len(coffeeList):
      deletedCoffee = coffeeList.pop(coffeeIndex)
      print(f"{deletedCoffee['name']}가 삭제되었습니다.\n")
    else:
      print("올바른 번호를 입력해주세요.")
  except ValueError:
    print("유효한 숫자를 입력해주세요.")


# 전체 커피 리스트 출력
def showCoffeeList():
  if not coffeeList:
    print("등록된 커피가 없습니다.")
    return
  for i, coffee in enumerate(coffeeList, start=1):
    print(
      f"[{i}] - {coffee['name']} | 가격: {coffee['price']}원 | 재고: {coffee['stock']}개")


# 판매 모드 함수
def sellCoffee():
  global totalSalesValue  # 함수 내에서 사용하기 위해 전역변수 선언

  while True:
    totalStock = sum(
        coffee['stock'] for coffee in coffeeList)  # 리스트 내 stock의 총합 계산

    if totalStock == 0:
      print("판매 종료. 총 매출 : " + str(totalSalesValue) + "원")
      break

    print("\n커피 종류를 선택해주세요")
    showCoffeeList()

    try:
      # 커피 선택
      selectCoffee = int(input("\n구매할 커피 번호를 입력하세요: ")) - 1
      if 0 <= selectCoffee < len(coffeeList):  # 범위 내 번호인지 확인
        selectedCoffee = coffeeList[selectCoffee]

        if selectedCoffee['stock'] > 0:
          print(
            f"\n{selectedCoffee['name']} | 가격 : {selectedCoffee['price']}원 입니다.")

          # 구매 수량 입력
          while True:
            try:
              quantity = int(input("몇 잔을 구매하시겠습니까? "))
              if quantity > 0 and quantity <= selectedCoffee['stock']:
                break
              else:
                print(f"잘못된 수량입니다. 재고: {selectedCoffee['stock']}잔")
            except ValueError:
              print("유효한 숫자를 입력해주세요.")

          # 결제
          try:
            userMoney = int(input("돈을 넣어주세요: "))
            calcMoney(userMoney, selectedCoffee, quantity)
          except ValueError:
            print("유효한 금액을 입력해주세요.")
        else:
          print("재고가 없습니다.")
      else:
        print("올바른 번호를 입력해주세요.")
    except ValueError:
      print("숫자를 입력해주세요.")


# 메인메뉴
while True:
  print("""
    [1] 관리자 모드
    [2] 장사 모드
    [3] 종료
    """)
  mode = input("모드를 선택하세요: ")

  if mode == "1":
    adminMode()
  elif mode == "2":
    sellCoffee()
  elif mode == "3":
    print("프로그램 종료")
    break
  else:
    print("올바르지 않은 입력입니다.")

```
