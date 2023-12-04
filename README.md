# rep1
//
//  AddShapeElementView.swift


import SwiftUI

struct AddShapeElementView: View {
    
    let size: CGSize
    
    @EnvironmentObject private var store: AppStore
    
    @State private var xPoint = ""
    @State private var yPoint = ""
    
    @State private var xControlPoint = ""
    @State private var yControlPoint = ""
    
    @State private var width = ""
    @State private var height = ""
    
    @State private var mutatedShapeID = UUID().uuidString
    
    var body: some View {
        Group {
            Section("change elements") {
                VStack(spacing: 20) {
                    addShape
                    addPointButton
                    addCurveButton
                    addRectangleButton
                    addEllipseButton
                }
            }
        }
    }
    
    private var addPointButton: some View {
        VStack {
            Button {
                addPoint()
            } label: {
                Text("Add Point")
            }
            HStack {
                Group {
                    TextField(text: $xPoint) {
                        Text("X")
                    }
                    TextField(text: $yPoint) {
                        Text("Y")
                    }
                }
                .frame(maxWidth: .infinity)
            }
        }
    }
    
    private var addShape: some View {
        HStack {
            Text("AddShape")
            Spacer()
            Button {
                mutatedShapeID = UUID().uuidString
                store.dispatch(.changeCollage(.addShape(.init(
                    elements: [],
                    zPosition: 1,
                    blendMode: .normal,
                    blur: .none,
                    adjustments: .defaultAdjustments,
                    transforms: .init(),
                    id: mutatedShapeID
                ))))
            } label: {
                Image(systemName: "plus.app")
                    .font(.largeTitle)
            }
        }

    }
    
    private var addCurveButton: some View {
        VStack {
            Button {
                addCurve()
            } label: {
                Text("Add Curve")
            }
            HStack {
                TextField(text: $xPoint) {
                    Text("endX")
                }
                TextField(text: $yPoint) {
                    Text("endY")
                }
            }
            HStack {
                TextField(text: $xControlPoint) {
                    Text("controlX")
                }
                TextField(text: $yControlPoint) {
                    Text("controlY")
                }
            }
        }
    }
    
    private var addRectangleButton: some View {
        VStack {
            Button {
                addRectangle()
            } label: {
                Text("Add Rectangle")
            }
            HStack {
                TextField(text: $xPoint) {
                    Text("X")
                }
                TextField(text: $yPoint) {
                    Text("Y")
                }
            }
            HStack {
                TextField(text: $width) {
                    Text("Width")
                }
                TextField(text: $height) {
                    Text("Height")
                }
            }
        }
    }
    
    private var addEllipseButton: some View {
        VStack {
            Button {
                addEllipse()
            } label: {
                Text("Add Elipse")
            }
            HStack {
                TextField(text: $xPoint) {
                    Text("X")
                }
                TextField(text: $yPoint) {
                    Text("Y")
                }
            }
            HStack {
                TextField(text: $width) {
                    Text("Width")
                }
                TextField(text: $height) {
                    Text("Height")
                }
            }
        }
    }
    
    private func addPoint() {
        guard let x = Double(xPoint),
              let y = Double(yPoint) else {
            return
        }
        
        dispatch(.point(.init(
            x: x / size.width,
            y: y / size.height
        )))
    }
    
    private func addCurve() {
        guard let endX = Double(xPoint),
              let endY = Double(yPoint),
              let controlX = Double(xControlPoint),
              let controlY = Double(yControlPoint)
        else {
            return
        }
        
        dispatch(.curve(
            endPoint: .init(x: endX / size.width,
                            y: endY / size.height),
            control: .init(x: controlX / size.width,
                           y: controlY / size.height)
        ))
    }
    
    private func addRectangle() {
        guard let rect = extractRect() else {
            return
        }
        
        dispatch(.rectangle(rect))
    }
    
    private func addEllipse() {
        guard let rect = extractRect() else {
            return
        }
        
        dispatch(.ellipse(rect))
    }
    
    private func extractRect() -> CGRect? {
        guard let x = Double(xPoint),
              let y = Double(yPoint),
              let width = Double(width),
              let height = Double(height)
        else {
            return nil
        }
        
        return .init(x: x / size.width,
                     y: y / size.height,
                     width: width / size.width,
                     height: height / size.height)
    }
    
    private func dispatch(_ element: ShapeElement) {
        store.dispatch(.changeCollage(.changeShape(
            .addElement(element), id: mutatedShapeID
        )))
    }
    
}

struct AddShapeElementView_Previews: PreviewProvider {
    static var previews: some View {
        List {
            AddShapeElementView(size: .init(side: 1000))
        }
        .environmentObject(AppStore.preview)
    }
}
