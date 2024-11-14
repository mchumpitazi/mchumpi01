from flask import Flask, render_template, request
import qrcode

app = Flask(__name__)

ticket_number = 1

@app.route('/', methods=['GET', 'POST'])
def generate_ticket():
    global ticket_number
    if request.method == 'POST':
        name = request.form['name']
        phone = request.form['phone']
        ticket_number += 1

        # Datos para el QR code
        qr_data = f"Ticket #{ticket_number}\nNombre: {name}\nTeléfono: {phone}"

        # Crear la imagen del QR code
        qr = qrcode.QRCode(
            version=1,
            error_correction=qrcode.constants.ERROR_CORRECT_L,
            box_size=10,
            border=4,
        )
        qr.add_data(qr_data)
        qr.make(fit=True)
        img = qr.make_image(fill_color="black", back_color="white")
        img.save("ticket_qr.png")

        return render_template('ticket.html', name=name, phone=phone, ticket_number=ticket_number, qr_code="ticket_qr.png")
    else:
        return render_template('form.html')

if __name__ == '__main__':
    app.run(debug=True)# mchumpi01