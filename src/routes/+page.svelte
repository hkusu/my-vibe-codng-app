<script>
	let userInfo = {
		height: 170,
		weight: 70,
		age: 30,
		experience: 3,
		averageScore: 100,
		swingSpeed: 'medium',
		preferredBrand: '',
		budget: 50000,
		clubType: 'driver'
	};

	let showRecommendation = false;
	let recommendation = null;

	const clubData = {
		driver: {
			low_speed: { name: 'テーラーメイド SIM2 MAX', shaft: 'フレックスA', price: 45000, reason: 'ヘッドスピードが遅めの方に最適な軽量設計' },
			medium_speed: { name: 'キャロウェイ EPIC SPEED', shaft: 'フレックスSR', price: 52000, reason: 'バランスの取れた飛距離性能' },
			high_speed: { name: 'タイトリスト TSi3', shaft: 'フレックスS', price: 58000, reason: 'ヘッドスピードが速い方向けの低スピン設計' }
		},
		iron: {
			beginner: { name: 'ピン G425', shaft: 'フレックスR', price: 80000, reason: '初心者に優しい大型ヘッド' },
			intermediate: { name: 'ミズノ JPX921', shaft: 'フレックスSR', price: 90000, reason: '中級者向けのバランス型' },
			advanced: { name: 'タイトリスト T100', shaft: 'フレックスS', price: 110000, reason: '上級者向けの操作性重視' }
		}
	};

	function getSpeedCategory(speed) {
		switch(speed) {
			case 'slow': return 'low_speed';
			case 'medium': return 'medium_speed';
			case 'fast': return 'high_speed';
			default: return 'medium_speed';
		}
	}

	function getSkillLevel(score, experience) {
		if (score > 110 || experience < 2) return 'beginner';
		if (score > 90 || experience < 5) return 'intermediate';
		return 'advanced';
	}

	function generateRecommendation() {
		let rec = {};
		
		if (userInfo.clubType === 'driver') {
			const speedCat = getSpeedCategory(userInfo.swingSpeed);
			rec = clubData.driver[speedCat];
		} else if (userInfo.clubType === 'iron') {
			const skillLevel = getSkillLevel(userInfo.averageScore, userInfo.experience);
			rec = clubData.iron[skillLevel];
		}

		const heightAdjustment = userInfo.height > 180 ? '+1インチ' : userInfo.height < 160 ? '-0.5インチ' : '標準';
		
		recommendation = {
			...rec,
			heightAdjustment,
			totalPrice: rec.price + (userInfo.budget > rec.price ? 0 : -5000),
			suitable: rec.price <= userInfo.budget
		};
		
		showRecommendation = true;
	}
</script>

<main class="container">
	<h1>🏌️‍♂️ ゴルフクラブ診断アプリ</h1>
	<p>あなたに最適なゴルフクラブとシャフトを提案します</p>

	<div class="form-section">
		<h2>基本情報</h2>
		
		<div class="form-group">
			<label for="height">身長 (cm):</label>
			<input type="number" id="height" bind:value={userInfo.height} min="140" max="200">
		</div>

		<div class="form-group">
			<label for="weight">体重 (kg):</label>
			<input type="number" id="weight" bind:value={userInfo.weight} min="40" max="120">
		</div>

		<div class="form-group">
			<label for="age">年齢:</label>
			<input type="number" id="age" bind:value={userInfo.age} min="10" max="80">
		</div>

		<div class="form-group">
			<label for="experience">ゴルフ歴 (年):</label>
			<input type="number" id="experience" bind:value={userInfo.experience} min="0" max="50">
		</div>

		<div class="form-group">
			<label for="averageScore">平均スコア:</label>
			<input type="number" id="averageScore" bind:value={userInfo.averageScore} min="70" max="150">
		</div>

		<div class="form-group">
			<label for="swingSpeed">スイングスピード:</label>
			<select id="swingSpeed" bind:value={userInfo.swingSpeed}>
				<option value="slow">遅め (40m/s未満)</option>
				<option value="medium">普通 (40-45m/s)</option>
				<option value="fast">速め (45m/s以上)</option>
			</select>
		</div>

		<div class="form-group">
			<label for="clubType">クラブタイプ:</label>
			<select id="clubType" bind:value={userInfo.clubType}>
				<option value="driver">ドライバー</option>
				<option value="iron">アイアン</option>
			</select>
		</div>

		<div class="form-group">
			<label for="budget">予算 (円):</label>
			<input type="number" id="budget" bind:value={userInfo.budget} min="10000" max="200000" step="5000">
		</div>

		<button class="recommend-btn" on:click={generateRecommendation}>
			おすすめクラブを診断する
		</button>
	</div>

	{#if showRecommendation && recommendation}
		<div class="recommendation-section">
			<h2>🎯 診断結果</h2>
			
			<div class="recommendation-card {recommendation.suitable ? 'suitable' : 'over-budget'}">
				<h3>{recommendation.name}</h3>
				<p class="shaft"><strong>推奨シャフト:</strong> {recommendation.shaft}</p>
				<p class="length"><strong>長さ調整:</strong> {recommendation.heightAdjustment}</p>
				<p class="price"><strong>価格:</strong> ¥{recommendation.price.toLocaleString()}</p>
				<p class="reason"><strong>選択理由:</strong> {recommendation.reason}</p>
				
				{#if !recommendation.suitable}
					<div class="budget-warning">
						⚠️ 予算オーバーです。中古品やセール時期を狙うことをおすすめします。
					</div>
				{:else}
					<div class="suitable-badge">
						✅ 予算内でおすすめです！
					</div>
				{/if}
			</div>

			<div class="additional-tips">
				<h3>📝 追加アドバイス</h3>
				<ul>
					{#if userInfo.experience < 2}
						<li>初心者の方は、まずレッスンを受けることをおすすめします</li>
					{/if}
					{#if userInfo.averageScore > 100}
						<li>スコア改善のため、ショートゲームの練習を重点的に行いましょう</li>
					{/if}
					{#if userInfo.height > 180}
						<li>身長が高めなので、ライ角の調整も検討してください</li>
					{:else if userInfo.height < 160}
						<li>身長が低めなので、軽めのクラブを選ぶと扱いやすいでしょう</li>
					{/if}
				</ul>
			</div>
		</div>
	{/if}
</main>

<style>
	.container {
		max-width: 800px;
		margin: 0 auto;
		padding: 2rem;
		font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
	}

	h1 {
		color: #2d5a27;
		text-align: center;
		margin-bottom: 0.5rem;
	}

	h1 + p {
		text-align: center;
		color: #666;
		margin-bottom: 2rem;
	}

	.form-section {
		background: #f8f9fa;
		padding: 2rem;
		border-radius: 10px;
		margin-bottom: 2rem;
	}

	.form-section h2 {
		color: #2d5a27;
		margin-bottom: 1.5rem;
	}

	.form-group {
		margin-bottom: 1rem;
	}

	label {
		display: block;
		margin-bottom: 0.5rem;
		font-weight: 600;
		color: #333;
	}

	input, select {
		width: 100%;
		padding: 0.75rem;
		border: 2px solid #ddd;
		border-radius: 5px;
		font-size: 1rem;
		transition: border-color 0.3s;
	}

	input:focus, select:focus {
		outline: none;
		border-color: #4CAF50;
	}

	.recommend-btn {
		background: linear-gradient(135deg, #4CAF50, #2d5a27);
		color: white;
		border: none;
		padding: 1rem 2rem;
		font-size: 1.1rem;
		font-weight: 600;
		border-radius: 25px;
		cursor: pointer;
		width: 100%;
		margin-top: 1rem;
		transition: transform 0.2s, box-shadow 0.2s;
	}

	.recommend-btn:hover {
		transform: translateY(-2px);
		box-shadow: 0 5px 15px rgba(76, 175, 80, 0.3);
	}

	.recommendation-section {
		animation: fadeIn 0.5s ease-in;
	}

	@keyframes fadeIn {
		from { opacity: 0; transform: translateY(20px); }
		to { opacity: 1; transform: translateY(0); }
	}

	.recommendation-card {
		background: white;
		padding: 2rem;
		border-radius: 10px;
		box-shadow: 0 4px 15px rgba(0,0,0,0.1);
		margin-bottom: 1.5rem;
		border-left: 5px solid #4CAF50;
	}

	.recommendation-card.over-budget {
		border-left-color: #ff6b6b;
	}

	.recommendation-card h3 {
		color: #2d5a27;
		margin-bottom: 1rem;
		font-size: 1.5rem;
	}

	.shaft, .length, .price, .reason {
		margin-bottom: 0.75rem;
		line-height: 1.5;
	}

	.price {
		font-size: 1.2rem;
		color: #2d5a27;
	}

	.suitable-badge {
		background: #d4edda;
		color: #155724;
		padding: 0.75rem;
		border-radius: 5px;
		margin-top: 1rem;
		font-weight: 600;
	}

	.budget-warning {
		background: #f8d7da;
		color: #721c24;
		padding: 0.75rem;
		border-radius: 5px;
		margin-top: 1rem;
		font-weight: 600;
	}

	.additional-tips {
		background: #e3f2fd;
		padding: 1.5rem;
		border-radius: 10px;
		border-left: 5px solid #2196F3;
	}

	.additional-tips h3 {
		color: #1976D2;
		margin-bottom: 1rem;
	}

	.additional-tips ul {
		margin: 0;
		padding-left: 1.5rem;
	}

	.additional-tips li {
		margin-bottom: 0.5rem;
		line-height: 1.5;
	}

	@media (max-width: 600px) {
		.container {
			padding: 1rem;
		}
		
		.form-section {
			padding: 1.5rem;
		}
	}
</style>
