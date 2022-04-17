# CurrentWeekView

```swift
struct CurrentWeekView: View {
    
    @StateObject var currentWeekViewModel = CurrentWeekViewModel()
    @Namespace var animation
    
    var body: some View {
        // MARK: CURRENT WEEK VIEW
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 10) {
                ForEach(currentWeekViewModel.currentWeek, id: \.self) { day in
//                                Text(day.formatted(date: .abbreviated, time: .omitted))
                    
                    
                    VStack(spacing: 10) {
                        
                        Text(currentWeekViewModel.extractDate(date: day, format: "dd"))
                            .font(.system(size: 15))
                            .fontWeight(.semibold)
                        
                        /// EEE will return day as Mon Tue ...
                        Text(currentWeekViewModel.extractDate(date: day, format: "EEE"))
                            .font(.system(size: 14))
                        
                        Circle()
                            .fill(.white)
                            .frame(width: 8, height: 8)
                            .opacity(currentWeekViewModel.isToday(date: day) ? 1 : 0)
                        
                    } //: VSTACK
                    // MARK: FOREGROUND COLOR
                    .foregroundStyle(currentWeekViewModel.isToday(date: day) ? .primary : .secondary)
                    .foregroundColor(currentWeekViewModel.isToday(date: day) ? .white : .black)
                    // Capsule Shape
                    .frame(width: 45, height: 90)
                    .background(
                        ZStack {
                            // MARK: Matched Geometry Effect
                            if currentWeekViewModel.isToday(date: day) {
                                Capsule()
                                    .fill(.black)
                                    .matchedGeometryEffect(id: "CURRENTDAY", in: animation)
                            }
                        }
                    )
                    .contentShape(Capsule())
                    .onTapGesture {
                        // Updating CurentDay
                        withAnimation {
                            currentWeekViewModel.currentDay = day
                        }
                    }
                    
                    
                }
            }
        }
        .padding(.horizontal)
    }
}
```

```swift
class CurrentWeekViewModel: ObservableObject {
    
    // MARK: CURRENT WEEK DAYS
    @Published var currentWeek: [Date] = []
    
    // MARK: Current Day
    @Published var currentDay: Date = Date()
    
    init() {
        fetchCurrentWeek()
    }
    
    func fetchCurrentWeek() {
        let today = Date()
        let calendar = Calendar.current
        let week = calendar.dateInterval(of: .weekOfMonth, for: today)
        guard let firstWeekDay = week?.start else {
            return
        }
        (1...7).forEach { day in
            if let weekday = calendar.date(byAdding: .day, value: day, to: firstWeekDay) {
                currentWeek.append(weekday)
            }
        }
    }
    
    // MARK: EXTRACING DATE
    func extractDate(date: Date, format: String) -> String {
        let formatter = DateFormatter()
        formatter.dateFormat = format
        return formatter.string(from: date)
    }
    
    // MARK: CHECK IF CURRENT DATE IS TODAY
    func isToday(date: Date) -> Bool {
        let calendar = Calendar.current
        return calendar.isDate(date, inSameDayAs: currentDay)
    }
    
    
}

```
