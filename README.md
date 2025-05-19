<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no"/>
<link rel="stylesheet" href="pag19.css">
<title>Cadastro de Produtos</title>
</head>
<body>
  <div class="container" role="main">
    <h1>Cadastro de Produtos</h1>
    <form id="productForm" aria-label="Formulário para cadastrar produtos" novalidate>
      <label for="name">Nome do Produto *</label>
      <input type="text" id="name" name="name" placeholder="Digite o nome do produto" required minlength="2" maxlength="100" autocomplete="off" />

      <label for="description">Descrição *</label>
      <textarea id="description" name="description" placeholder="Descrição detalhada" required minlength="5" maxlength="500"></textarea>

      <label for="price">Preço (R$) *</label>
      <input type="number" id="price" name="price" placeholder="0,00" step="0.01" min="0" required />

      <label for="category">Categoria *</label>
      <select id="category" name="category" required>
        <option value="" disabled selected>Selecione uma categoria</option>
        <option value="eletronicos">Eletrônicos</option>
        <option value="roupas">Roupas</option>
        <option value="alimentos">Alimentos</option>
        <option value="moveis">Móveis</option>
        <option value="outros">Outros</option>
      </select>

      <label for="quantity">Quantidade em estoque *</label>
      <input type="number" id="quantity" name="quantity" placeholder="0" min="0" required />

      <label for="image">Imagem do Produto</label>
      <input type="file" id="image" name="image" accept="image/*" aria-describedby="imageHelp" />
      <small id="imageHelp" style="font-size: 0.75rem; color: #666;">Opcional. Selecionar uma imagem para o produto.</small>

      <button type="submit">Cadastrar Produto</button>
    </form>

    <section class="products-list" aria-live="polite" aria-relevant="additions" aria-atomic="true" aria-label="Lista de produtos cadastrados">
      <h2 style="font-size:1.3rem; margin-top: 30px; color:#004aad;">Produtos Cadastrados</h2>
      <div id="productsContainer" aria-live="polite"></div>
    </section>
  </div>

<script>
  (() => {
    const form = document.getElementById('productForm');
    const productsContainer = document.getElementById('productsContainer');


    let products = JSON.parse(localStorage.getItem('products')) || [];

    function renderProducts() {
      productsContainer.innerHTML = '';
      if (products.length === 0) {
        productsContainer.innerHTML = '<p style="color:#666; font-style:italic;">Nenhum produto cadastrado.</p>';
        return;
      }
      products.forEach((p, idx) => {
        const card = document.createElement('article');
        card.className = 'product-card';
        card.tabIndex = 0;

       
        const img = document.createElement('img');
        if(p.imageDataUrl){
          img.src = p.imageDataUrl;
          img.alt = 'Imagem do produto ' + p.name;
        } else {
          img.alt = 'Imagem não disponível';
          img.src = 'https://via.placeholder.com/64x64?text=Sem+Foto';
        }
        card.appendChild(img);

        const info = document.createElement('div');
        info.className = 'product-info';

        const nameEl = document.createElement('strong');
        nameEl.textContent = p.name;
        info.appendChild(nameEl);

        const descEl = document.createElement('span');
        descEl.textContent = p.description;
        info.appendChild(descEl);

        const detailsEl = document.createElement('span');
        detailsEl.textContent = `Preço: R$ ${Number(p.price).toFixed(2)} | Categoria: ${p.category} | Estoque: ${p.quantity}`;
        info.appendChild(detailsEl);

        card.appendChild(info);
        productsContainer.appendChild(card);
      });
    }

    function saveProducts() {
      localStorage.setItem('products', JSON.stringify(products));
    }

    function capitalizeWords(str) {
      return str.replace(/\b\w/g, c => c.toUpperCase());
    }

    form.addEventListener('submit', (e) => {
      e.preventDefault();

     
      if (!form.checkValidity()) {
        form.reportValidity();
        return;
      }

      const name = form.name.value.trim();
      const description = form.description.value.trim();
      const price = parseFloat(form.price.value);
      const category = form.category.value;
      const quantity = parseInt(form.quantity.value, 10);

      if (price < 0 || quantity < 0) {
        alert('Preço e quantidade devem ser valores positivos.');
        return;
      }

      
      const fileInput = form.image;
      const file = fileInput.files[0];

      if(file){
        const reader = new FileReader();
        reader.onload = function(event){
          const imageDataUrl = event.target.result;
          addProduct(name, description, price, category, quantity, imageDataUrl);
        };
        reader.readAsDataURL(file);
      } else {
        addProduct(name, description, price, category, quantity, null);
      }
    });

    function addProduct(name, description, price, category, quantity, imageDataUrl) {
      products.push({
        name: capitalizeWords(name),
        description,
        price,
        category: capitalizeWords(category),
        quantity,
        imageDataUrl
      });
      saveProducts();
      renderProducts();
      form.reset();
      form.name.focus();
      alert('Produto cadastrado com sucesso!');
    }

    
    renderProducts();
  })();
</script>
</body>
</html>




  * {
    box-sizing: border-box;
  }
  body {
    margin: 50;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: #f5f7fa;
    color: #333;
    padding: 50px;
    display: flex;
    justify-content: center;
  }
  .container {
    width: 100%;
    max-width: 900px;
    background: white;
    border-radius: 10px;
    box-shadow: 0 8px 24px rgba(0,0,0,0.1);
    padding: 20px;
  }
  h1 {
    font-weight: 700;
    font-size: 1.8rem;
    text-align: center;
    margin-bottom: 10px;
    color: #085ac4;
  }
  form {
    display: flex;
    flex-direction: column;
  }
  label {
    margin-top: 12px;
    font-weight: 600;
    font-size: 0.9rem;
    color: #444;
  }
  input[type="text"],
  input[type="number"],
  input[type="file"],
  select,
  textarea {
    margin-top: 4px;
    padding: 10px;
    font-size: 1rem;
    border-radius: 6px;
    border: 1.8px solid #ddd;
    transition: border-color 0.3s ease;
    resize: vertical;
    font-family: inherit;
  }
  input[type="text"]:focus,
  input[type="number"]:focus,
  input[type="file"]:focus,
  select:focus,
  textarea:focus {
    border-color: #233449;
    outline: none;
  }
  textarea {
    min-height: 60px;
  }
  button {
    margin-top: 18px;
    padding: 12px;
    background-color: #2d88ff;
    color: white;
    font-weight: 700;
    font-size: 1.1rem;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }
  button:hover, button:focus {
    background-color: #00337a;
  }
  .products-list {
    margin-top: 30px;
  }
  .product-card {
    border: 1.3px solid #ddd;
    border-radius: 10px;
    padding: 12px;
    margin-bottom: 12px;
    box-shadow: 0 2px 6px rgba(0,0,0,0.05);
    display: flex;
    align-items: center;
    gap: 12px;
    background-color: #fafafa;
  }
  .product-card img {
    width: 64px;
    height: 64px;
    object-fit: contain;
    border-radius: 6px;
    background-color: white;
    border: 1px solid #ccc;
  }
  .product-info {
    flex: 1;
  }
  .product-info strong {
    display: block;
    font-size: 1rem;
    margin-bottom: 4px;
    color: #004aad;
  }
  .product-info span {
    font-size: 0.85rem;
    color: #555;
  }
  @media (max-width: 400px) {
    body {
      padding: 10px;
    }
    .container {
      padding: 15px;
    }
  }
